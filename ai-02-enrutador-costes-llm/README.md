# ai-02 · Enrutador de costes LLM (Cost Autopilot)

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 2: LLM Cost Autopilot* de la guía BASWE.

**Nivel:** 🟡 Intermedio · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `ai-01`, `dbx-03-transformaciones`, `dbx-10-databricks-sql`

## Qué vas a construir

Una capa de enrutado inteligente delante de varios modelos: analiza la complejidad de cada petición, la envía al **modelo más barato capaz de resolverla con calidad aceptable**, y verifica de forma asíncrona que la decisión fue correcta. La métrica estrella: **% de coste ahorrado** frente a mandarlo todo al modelo grande.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| GPT-4o / 4o-mini / Claude / Ollama | Foundation Model APIs (varios Llama/MPT) + External Models vía AI Gateway |
| Registro de modelos con precios | Tabla Delta `model_registry` (coste/token, latencia, tier) |
| Clasificador scikit-learn | Spark ML / scikit-learn en cluster, registrado en **MLflow** |
| SQLite de logs | Tabla Delta `routing_log` (audit trail completo) |
| FastAPI + docker-compose | **Model Serving endpoint** con pyfunc custom |
| Dashboard Streamlit/Grafana | **AI/BI Dashboard** + `system.billing.usage` |
| Worker async de verificación | **Lakeflow Job** continuo/triggered |

## Fases del proyecto

### Fase 1 — Registro de modelos y capa de abstracción (días 1–3)

- [ ] Tabla `main.ai_router.model_registry`: `model_id`, `endpoint`, `cost_per_1k_input`, `cost_per_1k_output`, `avg_latency_ms`, `quality_tier` (alto/medio/bajo).
- [ ] Popúlala con al menos 3 modelos disponibles en Foundation Model APIs de tu región (uno grande caro, uno medio, uno pequeño barato).
- [ ] Función unificada `send_request(prompt, model_id)` que llame al serving endpoint y devuelva siempre: texto, tokens in/out, latencia, coste calculado, model_id.
- [ ] Baseline: manda los mismos 10 prompts a todos los modelos y guarda salidas/costes/latencias.

### Fase 2 — Clasificador de complejidad (días 3–6)

- [ ] Define 3 tiers: T1 simple (reformatear, extraer, QA directo), T2 moderado (resumir, clasificar), T3 complejo (razonamiento multi-paso, juicio matizado).
- [ ] Escribe y etiqueta a mano 200+ prompts de ejemplo en los 3 tiers (tabla Delta `complexity_labeled`).
- [ ] Features: nº tokens, presencia de verbos ("analiza", "compara"), nº de restricciones, si incluye contexto, formato de salida.
- [ ] Entrena regresión logística o random forest (≥80% accuracy en holdout basta para V1) y regístrala en MLflow con Unity Catalog como registry.
- [ ] Mapa de enrutado: T1→modelo barato, T2→medio, T3→caro (configurable en tabla, no hardcodeado).

### Fase 3 — Verificación asíncrona de calidad (días 6–9)

- [ ] Tras responder al usuario, encola la petición (tabla Delta `verification_queue`).
- [ ] Job que re-ejecuta el prompt contra el modelo de tier alto y compara (acuerdo/LLM-as-judge).
- [ ] Si diverge: marca `routing_failure`, re-ejecuta con el modelo alto y loguea la escalación (coste extra, gap de calidad).
- [ ] Flywheel: cada fallo de enrutado se añade a `complexity_labeled`; re-entrena el clasificador semanalmente con un Job programado.

### Fase 4 — Logging y dashboard de costes (días 9–11)

- [ ] `routing_log`: timestamp, hash del prompt, tier, modelo elegido, coste, latencia, score de verificación, ¿escalado?.
- [ ] AI/BI Dashboard: coste/día vs coste-si-todo-al-modelo-caro ("ahorraste X €"), distribución de enrutado, distribución de calidad, tasa de escalación.
- [ ] Métrica estrella visible: **% de reducción de coste** manteniendo paridad de calidad.

### Fase 5 — API de servicio (días 11–13)

- [ ] Empaqueta el router como pyfunc de MLflow (`PythonModel` con `predict`) y despliégalo en un **Model Serving endpoint** serverless.
- [ ] El endpoint acepta el prompt y devuelve respuesta + metadata (modelo elegido, tier, por qué).
- [ ] Configuración sin redeploy: el mapa tier→modelo se lee de la tabla Delta en cada request (o cacheada con TTL).

### Fase 6 — Pulido (días 13–14)

- [ ] Load test: 500–1000 prompts variados contra el endpoint; informe final de ahorro.
- [ ] Case study: "reduje el coste de LLM un X% manteniendo Y% de paridad de calidad". Lidera con el número.

## Criterios de éxito

- [ ] El endpoint enruta sin que el caller elija modelo.
- [ ] El ahorro es calculable con una query SQL sobre `routing_log`.
- [ ] Los fallos de enrutado re-entrenan el clasificador (flywheel funcionando).
- [ ] Cero claves en el repo: los endpoints externos van con AI Gateway + secrets.

## Datos y URLs

- Prompts de entrenamiento: los escribes tú (200+).
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → Model Serving, AI Gateway; *Administration* → system tables de billing).

## ⚠️ Seguridad

- Precios de modelos en tabla Delta, no en código ni en Git.
- Si conectas OpenAI/Anthropic como external models, las API keys viven solo en la configuración de AI Gateway, nunca en notebooks commiteados.
