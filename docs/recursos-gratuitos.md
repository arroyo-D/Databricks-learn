# Recursos gratuitos — cómo hacer TODO el programa sin pagar

Tus recursos: **Databricks Free Edition** + **cuenta Azure gratuita** + **servidor propio (32 GB RAM / 28 núcleos)** + tu Mac. Esta guía adapta todos los tracks a esa realidad. Los límites de Free Edition citados aquí están verificados contra la documentación oficial a fecha de noviembre 2026 (las fuentes al final).

## 1. Tu inventario y para qué sirve cada pieza

| Recurso | Rol en el programa |
|---|---|
| **Databricks Free Edition** | Plataforma principal: notebooks serverless, Delta/Unity Catalog, Lakeflow Jobs, AI/BI + Genie, MLflow, Vector Search (1 endpoint), Apps (máx. 3) |
| **Servidor (32 GB / 28 núcleos)** | Kafka (cap-06), Postgres como origen relacional (cap-01), LLM local con Ollama (fallback del track ai), entrenamiento CPU (ml-01), warehouse con datos personales (an-04, privacidad) — ver [servidor-local.md](servidor-local.md) |
| **Azure (gratuito)** | Opcional. Créditos iniciales y servicios gratuitos 12 meses. El programa NO lo necesita: todo lo que el curso hace en Azure tiene alternativa gratuita (ver §4) |
| **Tu Mac** | Descargas de datasets (ver §3), desarrollo local con Databricks Connect (dbx-13), Asset Bundles (dbx-14) |
| **Kaggle / Colab (gratuitos)** | GPU T4 gratis para ml-02 (segmentación Marte) |

## 2. Límites de Free Edition que afectan al programa (verificados)

| Límite oficial | Impacto | Solución adoptada en este repo |
|---|---|---|
| Solo compute **serverless**; sin clusters clásicos ni GPU | dbx-00 pide "crear un cluster" | En Free Edition el compute se adjunta solo al ejecutar. La teoría de clusters (driver/executors, configs) se estudia igual: **cae en el examen**. dbx-00 incluye la adaptación |
| **Cuota diaria de uso** (fair usage): si la superas, el compute se apaga hasta el día siguiente | Entrenamientos largos | ml-01/ml-02: entrenamientos pesados en tu servidor o Kaggle/Colab; Databricks para tracking (MLflow) y evaluación |
| **Salida a Internet restringida** a dominios de confianza | Scraping y APIs desde notebooks (wr-04, an-01, an-03, ml-01, ml-02) | Patrón "descarga local → sube a Volume" (ver §3). Prueba siempre primero con un `requests.get` de una celda: si responde, adelante; si no, descarga en local |
| Jobs: **máx. 5 tareas concurrentes** | Trackers diarios/semanales (an-01, an-03, an-04) | Caben sin problema; no programes varios jobs a la misma hora |
| **1 pipeline activo por tipo** (Lakeflow SDP) | dbx-09, cap-01 | Trabaja los pipelines de uno en uno; pausa/elimina el anterior |
| Serving: **sin GPU, nº de endpoints limitado, sin provisioned throughput** | Track ai (serving de modelos propios), ml-02 | Preferir **inferencia batch** con `ai_query` sobre Foundation Models en vez de desplegar endpoints propios; ml-02 se sirve en HF Spaces |
| **Vector Search: 1 endpoint / 1 unidad** | ai-06, ai-07, cap-02 | Un solo índice activo cada vez; reutiliza el endpoint entre módulos |
| **Apps: máx. 3, se paran a las 24 h** | an-04, ml-02, caps | Para demos permanentes: Hugging Face Spaces (gratis). Apps de Databricks para la práctica |
| **Agent Bricks (Knowledge Assistant, Supervisor Agent) NO soportados** | cap-02, cap-05 | Se construyen "a mano" con Agent Framework/MLflow + Vector Search — de hecho aprendes más: los Bricks son la versión empaquetada de lo que montas tú |
| Sin Scala/R, solo Spark Connect (sin RDD API ni Spark UI) | Nada relevante | Todo el programa es Python/SQL; el examen certifica Python/SQL |
| **Solo uso personal no comercial** y Databricks puede entrenar modelos con tu contenido | Datos personales (an-04) | **Nunca subas exports personales (Spotify, banco) a Free Edition.** Esa parte se hace en local/tu servidor (ver an-04 y §5) |
| GPU limitada desbloqueable **verificando identidad con LinkedIn** (sujeta a disponibilidad, sin cuota publicada) | ml-02 | Opción A: verificación LinkedIn → AI Runtime. Opción B (recomendada, más fiable): Kaggle/Colab gratis |

## 3. Patrón "descarga local → Volume" (para la restricción de salida)

Cuando un notebook no pueda salir a Internet:

1. Descarga el fichero **desde tu Mac o el servidor** (navegador, `curl`, `kaggle` CLI, `huggingface-cli`).
2. Súbelo a un **UC Volume**: *Catalog → Volumes → Upload* o `databricks fs cp dataset.zip dbfs:/Volumes/main/datasets/raw/ --profile TU_WORKSPACE`.
3. En el notebook, lee del Volume como siempre.

Los módulos afectados (wr-04, an-01, an-03, ml-01, ml-02) indican en su README qué descargar. Las API keys (Kaggle, FRED) se configuran en tu máquina local para la descarga; en Databricks solo entra el dato, no la key.

## 4. Sustituciones Azure/servicios de pago

| Lo que usa el curso/vídeo | Alternativa gratuita adoptada |
|---|---|
| Azure Event Hub (cap-01, vídeo) | Generador de eventos propio escribiendo a un Volume + Auto Loader; o Kafka en tu servidor (cap-06) |
| Azure SQL (cap-01, vídeo) | Postgres en tu servidor como origen relacional, o tablas Delta simulando el maestro con CDC |
| Azure Databricks de pago | No lo crees: trabaja directamente en Free Edition (serverless). Azure Databricks consume crédito aunque el workspace sea "trial" |
| AWS S3/Glue/Redshift (vídeo cap-06) | UC Volumes + Unity Catalog + SQL Warehouse (ya incluido en cap-06) |
| PowerBI (vídeo cap-06) | AI/BI Dashboards + Genie |
| API de OpenAI/Anthropic de pago | `ai_query` sobre Foundation Models incluidos en la plataforma; fallback: Ollama en tu servidor (API compatible OpenAI) |
| GPU de pago (ml-02) | Kaggle Notebooks (30 h/semana T4) o Colab; o AI Runtime tras verificación LinkedIn (sujeto a disponibilidad) |

> 💡 Tu cuenta Azure déjala quieta: si algún día quieres practicar el vídeo de cap-01 "tal cual", los créditos iniciales de Azure (~200 $/30 días) cubren Event Hub + Azure SQL básicos sobradamente. Pero no es necesario para ningún módulo.

## 5. Privacidad: la regla que no se negocia

Los términos de Free Edition permiten a Databricks usar tu contenido para entrenar modelos. Consecuencia:

- ✅ En Free Edition: datos **públicos o sintéticos** únicamente (todo el repo está diseñado así).
- ❌ En Free Edition: exports de Spotify/banco/Google, documentos personales, emails.
- La variante "warehouse personal con tus datos" de `an-04` se hace **en tu servidor** con stack open source (Postgres/DuckDB + Streamlit local): mismas ideas (estrella, incremental, app), cero exposición. El módulo lo detalla.

## 6. Qué corre dónde (resumen por track)

| Track | Dónde |
|---|---|
| dbx-00 → dbx-14 | 100% Free Edition (serverless). Teoría de clusters desde el PDF |
| wr-01 → wr-08 | Free Edition; las descargas grandes, local → Volume |
| gov-01 → gov-06 | Free Edition (Unity Catalog incluido) |
| an-01 → an-04 | Free Edition; scraping vía §3; datos personales de an-04 en servidor |
| ai-01 → ai-15 | Free Edition con `ai_query`/Foundation Models; si un endpoint concreto no está disponible, fallback Ollama en servidor |
| ml-01 | Descarga Kaggle en local → Volume; entrenamiento CPU en serverless o en tu servidor, MLflow en Databricks |
| ml-02 | GPU: Kaggle/Colab (o AI Runtime con LinkedIn); demo en HF Spaces |
| cap-01 | Free Edition; maestro relacional en Postgres del servidor (opcional) |
| cap-02, cap-05 | Free Edition construyendo a mano lo que Agent Bricks empaqueta (no soportado en Free) |
| cap-06 | Kafka en tu servidor; la conexión Databricks→Kafka puede estar bloqueada por la restricción de salida: el módulo incluye el patrón file-drop (servidor empuja → Volume → Auto Loader) y la variante 100% local con PySpark open source |

## Fuentes (verificadas noviembre 2026)

- Limitaciones oficiales Free Edition: https://learn.microsoft.com/en-us/azure/databricks/getting-started/free-edition-limitations ✅
- Versión docs.databricks.com: https://docs.databricks.com/aws/en/getting-started/free-edition-limitations ✅
- Setup del servidor: [servidor-local.md](servidor-local.md)
