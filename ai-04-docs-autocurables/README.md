# ai-04 · Documentación técnica autocurable (Self-Healing Docs)

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 4: Self-Healing Technical Documentation* de la guía BASWE.

**Nivel:** 🟡 Intermedio · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `dbx-13-databricks-connect`, `dbx-14-asset-bundles`

## Qué vas a construir

Una GitHub Action que vigila este repo (o un fork de pruebas): cuando un PR cambia código, detecta qué secciones de la documentación Markdown han quedado **desactualizadas**, y genera automáticamente un PR con las correcciones (o comenta las secciones dudosas para revisión humana). Los embeddings y el análisis de similitud se hacen con Databricks.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| text-embedding-3-small | `ai_query` con endpoint de embeddings (BGE/GTE) de Foundation Model APIs |
| ChromaDB file-based | **Databricks Vector Search** (índice Delta Sync sobre los chunks de código/docs) |
| GPT-4o para staleness + rewrite | Model serving endpoint (Llama 70B) via `ai_query` |
| Docker para la Action | Action que ejecuta un **Lakeflow Job** o script con Databricks Connect |
| PyGithub + git diff | Igual (PyGithub) + Databricks como motor de análisis |

## Fases del proyecto

### Fase 1 — Mapa código↔docs (días 1–4)

- [ ] Parser del codebase: extrae unidades semánticas (firmas de función + docstrings, clases, endpoints, esquemas de config). ID estable: `ruta::nombre`.
- [ ] Parser de docs: secciones Markdown por encabezado, con la lista de referencias a código que mencionan.
- [ ] Grafo de enlaces: primero heurística (nombre de función mencionado en sección), luego embeddings: embebe chunks de código y secciones con el endpoint de embeddings y enlaza pares con similitud coseno > umbral.
- [ ] Persiste el grafo como tabla Delta `docs_code_map` (y exporta JSON al repo).

### Fase 2 — Detección de cambios (días 4–7)

- [ ] En cada PR: parsea el diff, mapea líneas cambiadas a chunks de código.
- [ ] Filtra cambios irrelevantes: comentarios, whitespace, refactors internos, tests.
- [ ] Consulta el grafo: secciones de docs enlazadas a chunks modificados = "sospechosas".
- [ ] Verificación de staleness con LLM: para cada sospechosa, manda código viejo + código nuevo + sección; que responda si sigue siendo precisa y qué falla exactamente.

### Fase 3 — Motor de reparación (días 7–10)

- [ ] Para cada sección confirmada como stale: LLM reescribe **solo las partes afectadas**, conservando estilo y tono.
- [ ] Segunda pasada de validación: ¿la corrección describe el código nuevo? ¿conserva lo que ya estaba bien? ¿estilo consistente?
- [ ] Dos modos: cambios simples (renombrado, default cambiado) → auto-fix; cambios complejos → draft con TODOs para revisión humana.

### Fase 4 — La GitHub Action (días 10–12)

- [ ] Action que en cada PR ejecuta el análisis (job de Databricks o script con Connect) y publica comentario resumen: "3 secciones verificadas, 1 auto-corregida (PR #N), 2 flagged".
- [ ] Auto-fix: rama nueva + PR con descripción clara del cambio y la evidencia.
- [ ] Flags: comentario en el PR original con enlaces a las secciones.

### Fase 5 — Prueba en repo real (días 12–13)

- [ ] Forkea un proyecto open source bien documentado (FastAPI, Pydantic), instala la Action, haz cambios que invaliden docs.
- [ ] Mide: true/false positives, false negatives, calidad de las correcciones. Publica los números en el README.

### Fase 6 — Pulido (días 13–14)

- [ ] Demo ≤3 min: push → Action corre → comentario → PR de corrección.
- [ ] README como propuesta interna, liderando con el problema ("toda documentación está perpetuamente desactualizada").

## Criterios de éxito

- [ ] Un renombrado de función genera PR de corrección sin intervención.
- [ ] Cambios cosméticos NO generan ruido (filtro de cambios irrelevantes).
- [ ] Métricas de precisión publicadas con datos reales del fork de prueba.

## Datos y URLs

- Corpus de prueba: fork público de un proyecto open source bien documentado (eliges uno activo de GitHub).
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → Vector Search).

## ⚠️ Seguridad

- El token de Databricks y el de GitHub van en **GitHub Secrets** del repo donde instales la Action.
- No subas al repo público chunks de código de terceros sin respetar su licencia (usa forks con licencia permisiva).
