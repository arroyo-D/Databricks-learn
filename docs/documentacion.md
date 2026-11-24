# Documentación oficial — índice completo de estudio

Toda la documentación que necesitas para el programa, organizada por tema, con **qué estudiar de cada área** (las características que debes poder explicar) y el enlace oficial verificado. Incluye features completas de la plataforma aunque tu Free Edition no las tenga todas: **el examen y las entrevistas preguntan por la plataforma completa**. Cuando algo no está en Free Edition, lo verás marcado con 💰 (disponible en workspaces de pago / para estudiar en teoría).

> 📌 Certificación objetivo: https://www.databricks.com/learn/certification/data-engineer-associate ✅ (descarga el *exam guide*: los porcentajes por dominio de ahí son los que usa el track `dbx`).
> 📌 Límites de tu edición: https://learn.microsoft.com/en-us/azure/databricks/getting-started/free-edition-limitations ✅

## 1. Plataforma y compute

| Tema | Qué estudiar (características clave) | Doc oficial |
|---|---|---|
| Introducción / Lakehouse | Qué es un lakehouse vs warehouse vs lake; arquitectura medallón; Data Intelligence Platform | https://docs.databricks.com/en/getting-started/index.html ✅ |
| Compute serverless | Cómo se adjunta compute serverless; diferencias vs clásico (sin Spark UI, sin RDD API, solo Python/SQL) | https://docs.databricks.com/en/compute/serverless.html ✅ |
| Clusters clásicos 💰 | All-purpose vs job clusters; single node; auto-termination; pools; Databricks Runtime y versiones LTS; políticas de cluster. **Cae en el examen aunque no puedas crearlos** | desde /en/compute/serverless.html navega a *Classic compute* ✅ |

## 2. Datos: almacenamiento y gobierno

| Tema | Qué estudiar | Doc oficial |
|---|---|---|
| Delta Lake | `_delta_log` y ACID; time travel y RESTORE; OPTIMIZE/Z-ORDER; Liquid Clustering; VACUUM; MERGE/COPY INTO; Change Data Feed | https://docs.databricks.com/en/delta/index.html ✅ |
| Unity Catalog | Jerarquía metastore→catalog→schema→table; permisos (GRANT); managed vs external tables; lineage; system.ai | https://docs.databricks.com/en/data-governance/unity-catalog/index.html ✅ |
| Volumes | Almacenar ficheros no tabulares gobernados; rutas `/Volumes/catalogo/esquema/volumen` | https://docs.databricks.com/en/volumes/index.html ✅ |
| Secrets | Scopes y `dbutils.secrets.get`; por qué nunca en código | https://docs.databricks.com/en/security/secrets/index.html ✅ |
| System tables 💰(parcial) | `system.billing.usage`, `system.lakeflow.*`: auditoría de costes y jobs | https://docs.databricks.com/en/administration-guide/system-tables/index.html ✅ |

## 3. Ingeniería de datos (core del examen)

| Tema | Qué estudiar | Doc oficial |
|---|---|---|
| Auto Loader | `cloudFiles` format; schema inference/evolution; `rescuedDataColumn`; notification vs directory listing | https://docs.databricks.com/en/ingestion/cloud-object-storage/auto-loader/index.html ✅ |
| Structured Streaming | Triggers (availableNow, processingTime, continuous); checkpointing; watermarks; exactly-once con Delta sink | https://docs.databricks.com/en/structured-streaming/index.html ✅ |
| Zerobus Ingest | Escritura directa a tablas UC sin conector Kafka (API push) | https://docs.databricks.com/en/ingestion/zerobus-ingest/index.html ✅ |
| Lakeflow Jobs | Tareas y dependencias; triggers schedule/file-arrival/continuous; parámetros; reintentos; if/else y For each | https://docs.databricks.com/en/jobs/index.html ✅ |
| Lakeflow Spark Declarative Pipelines (SDP) | `@dp.table`/`@dp.materialized_view`; streaming tables; expectations (expect/drop/fail); AUTO CDC (antes APPLY CHANGES); desarrollo vs producción | https://docs.databricks.com/en/ldp/index.html ✅ |
| Databricks SQL | SQL warehouses (serverless/pro/classic, sizing); queries, alerts; el warehouse 2X-Small de tu edición | https://docs.databricks.com/en/sql/index.html ✅ |

## 4. Consumo y BI

| Tema | Qué estudiar | Doc oficial |
|---|---|---|
| AI/BI Dashboards | Datasets vs consultas; parámetros; publicación y scheduling | https://docs.databricks.com/en/dashboards/index.html ✅ |
| Genie | Espacios, instrucciones, sinónimos; por qué PK/FK y COMMENT mejoran sus respuestas | https://docs.databricks.com/en/genie/index.html ✅ |
| Delta Sharing | Compartir datos sin copiar; open sharing; recipients | https://docs.databricks.com/en/delta-sharing/index.html ✅ |
| Lakehouse Federation 💰 | Catálogos externos sobre Postgres/MySQL/etc. sin ingestar | https://docs.databricks.com/en/query-federation/index.html ✅ |

## 5. DevOps y desarrollo

| Tema | Qué estudiar | Doc oficial |
|---|---|---|
| Databricks Connect | Ejecutar Spark remoto desde tu IDE local | https://docs.databricks.com/en/dev-tools/databricks-connect/index.html ✅ |
| Asset Bundles (DABs) | `databricks.yml`; deploy/run; CI/CD con GitHub Actions | https://docs.databricks.com/en/dev-tools/bundles/index.html ✅ |
| Databricks Apps | Apps (Streamlit/Dash) en el workspace; límites Free: 3 apps, parada a las 24 h | https://docs.databricks.com/en/dev-tools/databricks-apps/index.html ✅ |
| Lakebase | Postgres OLTP integrado en el lakehouse (1 proyecto en Free) | https://docs.databricks.com/en/oltp/index.html ✅ |

## 6. Machine Learning

| Tema | Qué estudiar | Doc oficial |
|---|---|---|
| MLflow (managed) | Experiments/runs, params/metrics/artifacts; Model Registry en UC; tracking desde fuera del workspace | https://docs.databricks.com/en/mlflow/index.html ✅ |
| Entrenamiento de modelos | Workflows de training en Databricks | https://docs.databricks.com/en/machine-learning/train-model/index.html ✅ |
| Feature Store 💰(parcial) | Feature tables en UC, online/offline | https://docs.databricks.com/en/machine-learning/feature-store/index.html ✅ |
| Lakehouse Monitoring 💰 | Métricas de deriva y calidad sobre tablas | https://docs.databricks.com/en/lakehouse-monitoring/index.html ✅ |

## 7. IA generativa (Mosaic AI)

| Tema | Qué estudiar | Doc oficial |
|---|---|---|
| Foundation Model APIs | Pay-per-token vs provisioned throughput; modelos hospedados por Databricks; límites en Free | https://docs.databricks.com/en/machine-learning/foundation-model-apis/index.html ✅ |
| AI Functions (`ai_query`, `ai_analyze_sentiment`, `ai_parse_document`...) | Llamar a LLMs desde SQL; batch inference gobernado | https://docs.databricks.com/en/large-language-models/ai-functions.html ✅ |
| Vector Search | Managed embeddings vs self-managed; sync index; límites Free: 1 endpoint/1 unidad | https://docs.databricks.com/en/generative-ai/vector-search.html ✅ |
| Model Serving | Endpoints CPU/GPU, scale-to-zero, inference tables; límites en Free | https://docs.databricks.com/en/machine-learning/model-serving/index.html ✅ |
| AI Gateway | Rate limits, budgets, guardrails, inference tables sobre endpoints | https://docs.databricks.com/en/ai-gateway/index.html ✅ |
| Agent Bricks 💰(no en Free) | Knowledge Assistant, Supervisor Agent, Information Extraction, Multi-Agent: qué empaqueta cada uno | https://docs.databricks.com/en/generative-ai/agent-bricks/index.html ✅ |
| Agentes: despliegue | ResponsesAgent con MLflow, `agents.deploy`, tracing | https://docs.databricks.com/en/generative-ai/deploy-agent.html ✅ |
| Agent Evaluation | LLM judges, métricas (correctness, faithfulness), eval sets | https://docs.databricks.com/en/generative-ai/agent-evaluation/index.html ✅ |
| Fine-tuning / Foundation Models 💰 | Customizar modelos base; cuándo fine-tuning vs RAG | https://docs.databricks.com/en/machine-learning/foundation-models/index.html ✅ |
| MCP en Databricks | Servidores MCP gestionados y agentes con herramientas | https://docs.databricks.com/en/generative-ai/mcp.html ✅ |

## Cómo usar este índice

1. Cuando un README diga "lee la documentación de X", ven aquí: el enlace exacto está verificado.
2. La columna "Qué estudiar" es tu checklist: si puedes explicar cada característica con tus palabras, dominas el tema.
3. 💰 = no disponible en Free Edition: estúdialo en teoría (examen/entrevistas) y practica el equivalente gratuito indicado en [recursos-gratuitos.md](recursos-gratuitos.md).
4. Cada módulo tiene además `preguntas/PREGUNTAS.md` (en TODOS los tracks) para autoevaluarte después de estudiar.
