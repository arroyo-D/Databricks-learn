# Databricks Learn

Programa completo de aprendizaje de Databricks, de cero a certificación y más allá, basado en:

1. **Curso**: Databricks Certified Data Engineer Associate (Udatademy) → track `dbx-XX`
2. **Libro**: *Practical Python Data Wrangling and Data Quality* (McGregor) → track `wr-XX`
3. **Libro**: *Designing Data Governance from the Ground Up* (Maffeo) → track `gov-XX`
4. **Guías BASWE**: *15 AI Engineering Projects* + *The AI Pivot Field Guide 2026* → track `ai-XX`
5. **Informe oficial**: *Databricks State of AI Agents* + vídeos de proyectos E2E → track `cap-XX` (proyectos integradores)

## Empieza aquí

👉 **[docs/ruta-de-aprendizaje.md](docs/ruta-de-aprendizaje.md)** — la ruta completa con prerrequisitos: qué necesitas antes de empezar (Fase 0), en qué orden hacer cada track y qué lecturas son obligatorias en cada punto. Diseñada para que no te saltes nada.

## Estructura

Cada módulo es una carpeta con:

- `README.md` → enunciado: objetivos, tareas con checklist y entregable
- `correccion/SOLUCION.md` → solución con código y puntos clave (consúltala DESPUÉS de intentarlo)
- `preguntas/PREGUNTAS.md` → preguntas de práctica originales estilo examen (solo track dbx)

## Track 1 — Certificación (dbx)

| # | Módulo | Dominio del examen |
|---|---|---|
| 00 | [Setup y fundamentos](dbx-00-setup) | Intelligence Platform 6% |
| 01 | [Unity Catalog setup](dbx-01-unity-catalog-setup) | Governance 15% |
| 02 | [Ingesta Zenviro](dbx-02-ingesta-zenviro) | Ingestion 21% |
| 03 | [Transformaciones Bronze→Gold](dbx-03-transformaciones) | Transformation 22% |
| 04 | [PySpark](dbx-04-python-spark) | Transformation 22% |
| 05 | [Structured Streaming](dbx-05-structured-streaming) | Ingestion / Troubleshooting |
| 06 | [Auto Loader](dbx-06-auto-loader) | Ingestion 21% |
| 07 | [Delta Lake](dbx-07-delta-lake) | Transformation / Optimization 10% |
| 08 | [Lakeflow Jobs](dbx-08-lakeflow-jobs) | Lakeflow Jobs 16% |
| 09 | [Lakeflow SDP](dbx-09-lakeflow-sdp) | Lakeflow Jobs 16% |
| 10 | [Databricks SQL](dbx-10-databricks-sql) | Platform / Production |
| 11 | [Gobernanza UC](dbx-11-gobernanza) | Governance 15% |
| 12 | [Delta Sharing + Federation](dbx-12-delta-sharing-federation) | Governance 15% |
| 13 | [Databricks Connect](dbx-13-databricks-connect) | CI/CD 10% |
| 14 | [Asset Bundles](dbx-14-asset-bundles) | CI/CD 10% |

## Track 2 — Data Wrangling & Data Quality (wr)

| # | Módulo |
|---|---|
| 01 | [Python fundamentos](wr-01-python-fundamentos) |
| 02 | [Calidad de datos: conceptos](wr-02-calidad-datos-conceptos) |
| 03 | [Ingesta de formatos](wr-03-ingesta-formatos) |
| 04 | [APIs y web scraping](wr-04-apis-web-scraping) |
| 05 | [Limpieza y transformación](wr-05-limpieza-transformacion) |
| 06 | [Código limpio y refactor](wr-06-codigo-limpio-refactor) |
| 07 | [Análisis exploratorio](wr-07-analisis-exploratorio) |
| 08 | [Visualización](wr-08-visualizacion) |

## Track 3 — Data Governance (gov)

| # | Módulo |
|---|---|
| 01 | [Framework de gobernanza](gov-01-framework-gobernanza) |
| 02 | [Roles y data stewards](gov-02-roles-stewards) |
| 03 | [Consejo y diccionario de datos](gov-03-consejo-diccionario-datos) |
| 04 | [Roadmap y evaluación](gov-04-roadmap-evaluacion) |
| 05 | [Desarrollo gobernado](gov-05-desarrollo-gobernado) |
| 06 | [Monitorización en producción](gov-06-monitorizacion-produccion) |

## Track 4 — AI Engineering sobre Databricks (ai)

Adaptación de los 15 proyectos de la guía BASWE *15 AI Engineering Projects* a la plataforma Databricks (Foundation Model APIs, AI Gateway, Vector Search, MLflow, Lakeflow, Genie, Agent Framework). Nivel intermedio → avanzado. Cada proyecto está pensado para 10–14 días a 2–3 h/día; no hacen falta todos: 2–3 bien pulidos valen más que 15 a medias.

| # | Proyecto | Nivel | Tecnología Databricks clave |
|---|---|---|---|
| 01 | [Detección de regresiones LLM (CI/CD)](ai-01-eval-regresiones-llm) | 🟡 | `ai_query`, Lakeflow Jobs, MLflow, AI/BI |
| 02 | [Enrutador de costes LLM](ai-02-enrutador-costes-llm) | 🟡 | Model Serving, MLflow, system tables billing |
| 03 | [Forense de pipelines de IA](ai-03-forense-pipelines-ia) | 🔴 | MLflow Tracing, Delta, judge loops |
| 04 | [Docs autocurables](ai-04-docs-autocurables) | 🟡 | Vector Search, GitHub Actions, Connect |
| 05 | [Arbitraje multi-crítico](ai-05-arbitraje-salidas-llm) | 🔴 | Multi-endpoint serving, AI Gateway, LangGraph |
| 06 | [RAG híbrido](ai-06-rag-hibrido-vector-search) | 🔴 | Vector Search (híbrido), Agent Evaluation |
| 07 | [Caché semántica LLM](ai-07-cache-semantica-llm) | 🟡 | Vector Search, Model Serving, AI/BI |
| 08 | [Text-to-SQL con guardrails](ai-08-text-to-sql-guardrails) | 🟡 | Genie, SQL Warehouse, permisos UC |
| 09 | [Prompt registry + A/B testing](ai-09-prompt-registry-ab-testing) | 🔴 | MLflow Prompt Registry (UC), scipy |
| 10 | [Fine-tuning en dominio](ai-10-finetuning-dominio) | 🔴 | Foundation Model Training, MLflow |
| 11 | [AI Gateway gobernado](ai-11-ai-gateway-gobernado) | 🔴 | AI Gateway, inference tables, External Models |
| 12 | [Feature flags para IA](ai-12-feature-flags-ia) | 🔴 | Lakehouse Monitoring, streaming, rollback auto |
| 13 | [Eval dataset automático](ai-13-eval-dataset-automatico) | 🟡 | Inference tables, clustering, auto-labeling |
| 14 | [Procesador de documentos](ai-14-procesador-documentos) | 🔴 | `ai_parse_document`, Auto Loader, DQX |
| 15 | [Orquestación multi-agente](ai-15-orquestacion-agentes) | 🔴 | Agent Framework, UC Functions, MLflow Tracing |

## Track 5 — Proyectos integradores (cap)

Proyectos grandes tipo portfolio real. Cada uno **une varios módulos** de los tracks anteriores en un solo sistema completo, inspirado en los casos de uso más frecuentes del informe oficial *Databricks State of AI Agents* (datos de +20.000 organizaciones) y en los vídeos de proyectos end-to-end enlazados abajo. Son la prueba final: si completas uno de estos, dominas todo lo que integra.

| # | Proyecto | Une | Inspiración |
|---|---|---|---|
| 01 | [Plataforma de restaurantes E2E](cap-01-plataforma-restaurantes) | dbx-01→10 | Vídeo Zero-To-Expert: streaming + batch + medallion + sentimiento IA + AI/BI |
| 02 | [Knowledge Assistant empresarial](cap-02-knowledge-assistant) | ai-06, ai-01, ai-13 | Knowledge Assistant (Agent Brick); 40% de casos de uso = experiencia de cliente |
| 03 | [Fábrica de extracción de documentos](cap-03-fabrica-extraccion-documentos) | ai-14, ai-03 | Information Extraction: 31% del uso de Agent Bricks |
| 04 | [Soporte al cliente con IA gobernada](cap-04-soporte-cliente-gobernado) | ai-02, ai-07, ai-09, ai-12 | Clasificación de soporte (top 3); multi-modelo (78% de empresas) |
| 05 | [Multiagente supervisor gobernado](cap-05-multiagente-supervisor-gobernado) | ai-15, ai-05, ai-11 | Supervisor Agent nº1 (37%); gobernanza 12x, evaluación 6x |

### Vídeos de referencia

- [Databricks End-To-End Project | Zero-To-Expert](https://www.youtube.com/watch?v=vy4G86q1rQY) — plataforma de restaurantes con Event Hub, Azure SQL, UC, SDP, Lakeflow Connect CDC, sentimiento con Mosaic AI y AI/BI (índice de capítulos dentro de `cap-01`).
- [Full End-to-End Data Engineering Project in Databricks](https://www.youtube.com/watch?v=SSz7HiyQN1M) — proyecto E2E medallion (refuerzo de `cap-01`).

### Orden sugerido global

1. `dbx-00` → `dbx-14` (base + certificación)
2. `wr-01` → `wr-08` en paralelo si te falta Python
3. `ai-01` → elegir 2–3 del track ai
4. `cap-01` → `cap-05` como proyectos finales de portfolio

## Fuentes de datos

Los ejercicios usan datasets públicos verificados: ver [docs/fuentes-de-datos.md](docs/fuentes-de-datos.md).

## ⚠️ Seguridad (repo público)

- **Nunca** commitees tokens, API keys, contraseñas ni archivos `.databrickscfg`.
- Las credenciales van en Databricks Secrets (`dbutils.secrets`), GitHub Secrets o en variables de entorno locales.
- Todos los tokens/keys que aparecen en los ejemplos son **placeholders** (`dapiXXXX...`, `TU_CLAVE`).
- Los datasets de los ejercicios AI son **sintéticos o autocurados**: no subas emails, documentos ni logs con datos reales.
- Revisa `git diff --cached` antes de cada push y mantén el `.gitignore` actualizado.
