# ai-09 · Registro de prompts y plataforma de A/B testing

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 9: Prompt Versioning and A/B Testing Platform* de la guía BASWE.

**Nivel:** 🔴 Avanzado · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `ai-01`, `dbx-08-lakeflow-jobs`

## Qué vas a construir

Una plataforma que trata los prompts como **artefactos versionados** (como el código): despliega múltiples variantes simultáneamente, divide el tráfico entre ellas, mide rendimiento con métricas custom y declara ganadores con **significancia estadística**. Es feature-flagging + experimentación para features LLM.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| PostgreSQL registry | **MLflow Prompt Registry** (prompts como entidades versionadas en Unity Catalog) + tablas Delta |
| Serving con split de tráfico | Model Serving endpoint (pyfunc) con asignación consistente por hash |
| scipy.stats | Igual, en notebooks/Jobs de Databricks |
| Dashboard React | AI/BI Dashboard + vistas sobre Delta |
| Webhook alertas | SQL Alerts + Lakeflow Jobs |

## Fases del proyecto

### Fase 1 — Registro de prompts (días 1–3)

- [ ] Registra prompts en MLflow Prompt Registry dentro de UC: nombre, versiones auto-incrementadas, texto del system prompt, few-shots, parámetros (temperature, max_tokens), commit message.
- [ ] Tablas Delta complementarias: `prompt_audit` (quién activó qué versión, cuándo, por qué) y `prompt_templates` con variables `{{variable}}` validadas en tiempo de serving.
- [ ] Rollback = cambiar qué versión está marcada como `production`, sin deploy. Todo activación/desactivación queda auditada.

### Fase 2 — Motor de experimentos (días 3–6)

- [ ] Esquema de experimento: nombre, prompt, 2+ variantes (versiones), split de tráfico (50/50, 80/10/10), métrica primaria, tamaño muestral objetivo, estado (draft/running/completed/cancelled).
- [ ] Splitter: asignación por hash consistente de user_id/session_id → el mismo usuario siempre ve la misma variante. Log de cada asignación.
- [ ] Endpoint de serving: recibe prompt_id + variables → resuelve versión activa o variante de experimento → rellena plantilla → llama al LLM → log completo (variante incluida). El caller no sabe que hay experimento.
- [ ] Guardrails de experimento: auto-stop si error rate > umbral o si una variante es significativamente peor; notificación al owner.

### Fase 3 — Métricas y análisis estadístico (días 6–9)

- [ ] Framework de métricas pluggables: built-in (latencia, tokens, coste, error rate) + custom (LLM-as-judge 1–5, accuracy de tarea, feedback de usuario). Métricas custom corren asíncronas tras responder.
- [ ] Estadística continua: media/varianza por variante, t-test (o Mann-Whitney), p-value, intervalo de confianza, mínimo efecto detectable dado el n actual.
- [ ] Dashboard por experimento: comparación con ICs, progreso hacia el n objetivo, series temporales, estado "ganador / sin ganador / inconcluso".
- [ ] Declaración automática de ganador al alcanzar significancia (95%): marca, notifica, y opcionalmente auto-promociona tras 24h de hold.

### Fase 4 — Interfaz de gestión (días 9–11)

- [ ] UI de experimentos: crear desde el registry, configurar splits y métricas, monitorizar, promover ganadores con un clic.
- [ ] Comparador rápido: dos versiones cualesquiera, mismos inputs de prueba, salidas lado a lado con scores.
- [ ] Audit log completo: "¿quién cambió el prompt que rompió la feature el martes?" respondible con una query.

### Fase 5 — Integración y demo (días 11–13)

- [ ] Escenario: clasificador de emails con 3 variantes (zero-shot vs few-shot vs chain-of-thought), 500+ requests sintéticos, convergencia a ganador.
- [ ] Tests de integración: versionado y rollback, consistencia del splitter, cálculo de significancia contra distribuciones conocidas, auto-stop por error rate.

### Fase 6 — Pulido (días 13–14)

- [ ] Demo ≤4 min: crear prompt → lanzar experimento → métricas convergiendo → ganador declarado con estadística → promoción a producción → audit log.
- [ ] Narrativa: "experimentación con rigor de feature flags para desarrollo LLM; resultados estadísticamente significativos en vez de adivinar".

## Criterios de éxito

- [ ] El mismo usuario siempre cae en la misma variante (consistencia verificable).
- [ ] Una variante deliberadamente mala es auto-parada antes del n objetivo.
- [ ] El ganador se declara con p-value e IC visibles, no con "parece mejor".

## Datos y URLs

- Requests sintéticos: generados por ti (variantes del corpus de emails de `ai-01`).
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → MLflow, Prompt Registry).

## ⚠️ Seguridad

- Nada de claves en el registry: los prompts son texto + parámetros; las credenciales viven en el serving endpoint.
- No experimentes con datos de usuarios reales en este proyecto de aprendizaje.
