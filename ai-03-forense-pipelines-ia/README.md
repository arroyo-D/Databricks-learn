# ai-03 · Forense de fallos en pipelines de IA (Failure Forensics)

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 3: Failure Forensics Tool for AI Pipelines* de la guía BASWE.

**Nivel:** 🔴 Avanzado · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `ai-01`, `dbx-05-structured-streaming`

## Qué vas a construir

Una capa de observabilidad para un pipeline LLM de 4 pasos (intake → extracción → clasificación → resumen de documentos) que traza **cada paso intermedio**, identifica exactamente dónde se origina un fallo cuando la salida final es mala, y convierte los fallos confirmados en nuevos casos de evaluación. Es un mini-LangSmith construido sobre MLflow Tracing + Delta.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| OpenAI API | `ai_query()` / Foundation Model APIs |
| OpenTelemetry + spans custom | **MLflow Tracing** (`@mlflow.trace`), spans por paso |
| SQLite + JSON traces | MLflow experiments + tabla Delta `failure_cases` |
| React trace explorer | MLflow Trace UI + AI/BI Dashboard de analítica de fallos |
| API REST para flagging | Notebook/App + webhook |

## Fases del proyecto

### Fase 1 — Pipeline multi-paso (días 1–3)

- [ ] 4 pasos como funciones aisladas con tipos Pydantic: `intake(doc)`, `extract(text)`, `classify(text)`, `summarize(text, doc_type)`.
- [ ] Corpus de 30–50 documentos sintéticos (contratos, facturas, informes, correspondencia) en un Volume.
- [ ] Inyecta modos de fallo realistas: contrato sin fechas, factura multi-moneda, documento ambiguo entre dos tipos.

### Fase 2 — Capa de trazado (días 3–6)

- [ ] Activa `mlflow.autolog()` o decora cada paso con `@mlflow.trace`: cada paso captura input, output, prompt enviado, respuesta cruda, tokens y latencia.
- [ ] Cada ejecución del pipeline = un trace con `trace_id`; cada paso = un span hijo.
- [ ] Tras cada llamada LLM, pide al modelo un `confidence` 1–5 y guárdalo como atributo del span.
- [ ] Exporta los traces a Delta (`traces_flat`: trace_id, span, step, status, confidence, latency, tokens).

### Fase 3 — Analizador de causa raíz hacia atrás (días 6–9)

- [ ] Cuando una salida se marca como mala: recorre los spans **de atrás hacia delante**.
- [ ] En cada paso, un LLM-as-judge puntúa: "¿es esta salida una transformación razonable de su entrada?". El primer paso con caída significativa = sospechoso principal.
- [ ] Taxonomía de fallos: `extraction_hallucination`, `misclassification`, `propagation_error`, `prompt_failure`, `context_loss`.
- [ ] Cadena de evidencia estructurada: "Paso 2 (Extracción) alucinó la entidad X que no aparece en el documento fuente; se propagó al Paso 4...", con los pares input/output como prueba.

### Fase 4 — Explorador visual de traces (días 9–11)

- [ ] Vista del pipeline como nodos coloreados: verde (OK), amarillo (baja confianza), rojo (causa raíz identificada). Usa la MLflow Trace UI para el detalle de cada span.
- [ ] Vista diff: qué recibió el paso vs qué produjo vs qué debería haber producido.
- [ ] Interfaz de flagging: botón/función `flag_bad_output(trace_id)` que dispara el análisis y permite confirmar o corregir el diagnóstico.

### Fase 5 — Bucle de feedback a evaluación (días 11–13)

- [ ] Cada fallo confirmado genera automáticamente un caso de eval: input original, paso fallido, salida mala, corrección humana, categoría del fallo → tabla `failure_cases`.
- [ ] Job periódico que re-ejecuta los `failure_cases` contra el pipeline actual: ¿siguen fallando o ya están resueltos?
- [ ] Dashboard: tipos de fallo más comunes, paso que más falla, tasa de fallo en el tiempo, tiempo medio hasta diagnóstico.

### Fase 6 — Pulido (días 13–14)

- [ ] Demo: 50 documentos, 8–10 fallos variados; flag → diagnóstico → dataset que crece.
- [ ] Doc de arquitectura: "reduje el tiempo medio de diagnóstico de horas de depuración manual a segundos".

## Criterios de éxito

- [ ] Ante una salida mala, el sistema señala el paso correcto sin depuración manual.
- [ ] Los fallos confirmados reaparecen como casos de eval automáticamente.
- [ ] La analítica muestra qué paso falla más (priorización de ingeniería).

## Datos y URLs

- Corpus: documentos sintéticos que generas tú (o adapta CSVs de [../docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)).
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → MLflow Tracing).

## ⚠️ Seguridad

- No uses documentos reales con datos personales en el corpus: repo público.
- Los traces pueden contener el documento completo: guárdalos en tu metastore, no en Git.
