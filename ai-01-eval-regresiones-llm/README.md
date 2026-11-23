# ai-01 · Detección de regresiones en prompts y modelos (CI/CD para LLMs)

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 1: Model Regression Detection System* de la guía BASWE *15 AI Engineering Projects*.

**Nivel:** 🟡 Intermedio · **Duración estimada:** 10–12 días (2–3 h/día)
**Prerrequisitos:** `dbx-02-ingesta-zenviro`, `dbx-07-delta-lake`, `dbx-08-lakeflow-jobs`

## Qué vas a construir

Un pipeline de evaluación continua para una feature LLM: un **clasificador de emails de soporte** (categoría: `billing`, `technical`, `account`, `general` + resumen de una frase). Cada vez que cambie un prompt o el modelo, el sistema:

1. Ejecuta un **golden dataset** curado a mano contra la feature.
2. Puntúa cada salida en varias dimensiones (exactitud, calidad del resumen, latencia, tokens).
3. Compara contra la ejecución anterior y detecta **regresiones y mejoras**.
4. Alerta (Databricks SQL Alert + webhook a Slack) si la caída supera umbrales configurables.
5. Bloquea merges en GitHub si hay regresión crítica.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| OpenAI API (gpt-4o / mini) | Foundation Model APIs vía `ai_query()` (serverless, pay-per-token) |
| Prompts YAML en `/prompts` | YAML versionados en un **Volume** de Unity Catalog + Git |
| Golden dataset JSON | Tabla Delta `main.ai_eval.emails_golden` (versionada con time travel) |
| Eval runner + LLM-as-judge | Notebook/Job con `ai_query` batch + `mlflow.evaluate()` |
| SQLite + ficheros JSON | Tablas Delta `eval_runs`, `eval_results` |
| Reporte HTML + Slack | **AI/BI Dashboard** + Databricks SQL Alert con webhook |
| GitHub Actions + Docker | GitHub Actions que dispara un **Lakeflow Job** (Asset Bundles) |

## Fases del proyecto

### Fase 1 — La feature bajo test (días 1–2)

- [ ] Crea el esquema `main.ai_eval` (reusa el metastore de `dbx-01`).
- [ ] Escribe el prompt del clasificador como YAML (`version`, `timestamp`, `system_prompt`, `few_shot[]`) y súbelo a un Volume: `/Volumes/main/ai_eval/prompts/email_classifier_v1.yaml`.
- [ ] Implementa la feature en un notebook: lee el YAML del Volume y llama a `ai_query()` con el modelo `databricks-meta-llama-3-3-70b-instruct` (o el disponible en tu región).
- [ ] Define el contrato de salida: JSON con `category` y `summary`. Valida con Pydantic en el notebook.

### Fase 2 — Golden dataset (días 2–4)

- [ ] Escribe **a mano** 50–100 emails de soporte realistas en las 4 categorías (NO los generes con LLM: son tu ground truth).
- [ ] Incluye edge cases etiquetados con `expected_difficulty`: ambiguos, muy cortos, con faltas, bilingües, sarcásticos.
- [ ] Cárgalos como tabla Delta con columnas: `case_id`, `email_text`, `expected_category`, `ideal_summary`, `difficulty`, `notes`.
- [ ] Documenta la versión del dataset (usa `DESCRIBE HISTORY` para ver el versionado Delta).

### Fase 3 — Motor de evaluación (días 4–7)

- [ ] Runner: función que toma una versión de prompt, ejecuta todos los casos con `ai_query` y guarda salidas crudas.
- [ ] Scoring multidimensional por caso: acierto de categoría (binario), relevancia del resumen (LLM-as-judge 1–5 con `ai_query`), latencia, tokens de entrada/salida.
- [ ] Persiste cada ejecución en `eval_runs` (metadata: prompt_version, model, timestamp) y `eval_results` (una fila por caso).
- [ ] Comparación contra el run anterior: delta de pass rate, delta por categoría, casos que pasaron de OK→KO (regresiones) y de KO→OK (mejoras).
- [ ] Umbrales configurables: warning si el delta supera 3%, crítico si supera 8%.

### Fase 4 — Reportes y alertas (días 7–9)

- [ ] AI/BI Dashboard sobre `eval_runs`: scorecard del último run vs baseline, gráfico de tendencia de los últimos N runs, tabla de regresiones con salida antigua vs nueva.
- [ ] Databricks SQL Alert: query que detecta regresión crítica → webhook a Slack (o email) con los números clave.
- [ ] Detección de drift lento: media móvil de 7 runs; alerta si cae por debajo del umbral aunque ningún run individual lo hiciera.

### Fase 5 — CI/CD (días 9–11)

- [ ] Empaqueta el pipeline como **Lakeflow Job** con Asset Bundles (reusa lo aprendido en `dbx-14`).
- [ ] GitHub Action: en PRs que modifiquen `prompts/`, ejecuta el job vía Databricks CLI, espera el resultado y comenta el PR con pass/warn/fail.
- [ ] Bloquea el merge si el estado es crítico (exit code distinto de 0).
- [ ] README del módulo escrito como documentación interna: qué hace, cómo añadir casos al golden dataset, cómo ajustar umbrales, decisiones de arquitectura.

### Fase 6 — Pulido (días 11–12)

- [ ] Vídeo corto (≤3 min): cambiar un prompt, disparar la eval, ver la alerta y el dashboard.
- [ ] Escribe el párrafo narrativo: "CI/CD para el comportamiento del modelo", con una decisión de diseño defendida (p. ej. por qué separas drift lento de regresión por run).

## Criterios de éxito

- [ ] Cambiar un prompt a peor dispara alerta y bloquea el PR.
- [ ] El dashboard muestra tendencia y regresiones sin tocar código.
- [ ] El golden dataset es 100% manual y versionado en Delta.
- [ ] Todo el historial de evaluaciones es consultable con SQL.

## Datos y URLs

- Los emails del golden dataset los escribes tú (es el objetivo del ejercicio).
- Documentación oficial: [docs.databricks.com](https://docs.databricks.com/) (secciones *AI and machine learning* y *Lakeflow Jobs*).
- Fuentes de datos generales del programa: [../docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md).

## ⚠️ Seguridad

- El token de Databricks para la GitHub Action va en **GitHub Secrets** (`DATABRICKS_TOKEN`), nunca en el workflow en claro.
- No uses emails reales de clientes en el golden dataset: este repo es público.
