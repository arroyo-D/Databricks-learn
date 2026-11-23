# ai-14 · Procesador de documentos multimodal (OCR + extracción + validación)

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 14: Multi-Modal Document Processor* de la guía BASWE.

**Nivel:** 🔴 Avanzado · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `dbx-06-auto-loader`, `dbx-09-lakeflow-sdp`, `ai-03`

## Qué vas a construir

Un pipeline que acepta cualquier documento (PDF nativo, PDF escaneado, imagen), extrae el texto, estructura los datos con LLM según esquemas por tipo de documento, valida cada campo contra **reglas de negocio configurables**, y enruta los casos de baja confianza a revisión humana — con Auto Loader ingiriendo y Delta como sistema de registro.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| Tesseract + EasyOCR + GPT-4o vision | **`ai_parse_document`** (Databricks) + visión multimodal vía serving endpoint para casos difíciles |
| Celery + Redis | **Auto Loader + Lakeflow Declarative Pipelines** (procesamiento por lotes/streaming) |
| instructor + Pydantic | `ai_query` con JSON schema + validación Pydantic |
| Reglas de negocio custom | Validaciones Pydantic + **expectations** de pipelines / DQX |
| Review UI React | Databricks App o dashboard de revisión |

## Fases del proyecto

### Fase 1 — Ingesta y extracción de texto (días 1–3)

- [ ] Auto Loader vigilando un Volume: llegan PDFs e imágenes → cola de procesamiento (`documents_raw`).
- [ ] Extracción: `ai_parse_document` para PDFs (nativos y escaneados); para imágenes sueltas o casos donde la extracción falla, endpoint multimodal (Llama vision) que transcribe preservando estructura.
- [ ] Detección automática de estrategia: densidad de texto por página (nativo vs escaneado) → log de qué estrategia se aplicó y confianza por página.
- [ ] Preprocesado para escaneados difíciles: deskew/binarización con Pillow antes de OCR; registra qué pasos mejoraron la confianza.

### Fase 2 — Motor de extracción estructurada (días 3–6)

- [ ] Esquemas Pydantic por tipo: factura (proveedor, nº, líneas, impuestos, total, vencimiento), contrato (partes, fecha, vigencia, obligaciones, cláusulas de terminación).
- [ ] Clasificador de tipo de documento (`ai_query` sobre el texto) → carga del esquema correspondiente.
- [ ] Extracción con JSON schema estricto + instrucción explícita: "solo información presente en el texto; sin inferencias ni valores por defecto".
- [ ] Documentos largos: chunk por secciones, extracción independiente y merge; conflictos (dos chunks, mismo campo, valores distintos) → flag con ambas fuentes.
- [ ] Confianza por campo: match exacto vs fuzzy en el texto, auto-confianza del LLM, acuerdo entre chunks, validación de formato.

### Fase 3 — Validación y reglas de negocio (días 6–9)

- [ ] Validación de tipos: fechas válidas y plausibles, importes positivos, campos requeridos, consistencia cruzada (líneas suman al total).
- [ ] Reglas de negocio: ¿el proveedor existe en la tabla maestra? ¿el total está en rango típico para ese proveedor? ¿la fecha de efecto es futura?
- [ ] Detector de anomalías: comparación contra histórico por tipo/origen (importes atípicos, proveedores nuevos, patrones de fecha raros).
- [ ] Enrutado por confianza: alta → auto-aprobado; media → revisión rápida; baja/regla violada → cola de revisión completa.

### Fase 4 — Interfaz de revisión humana (días 9–11)

- [ ] Vista lado a lado: documento renderizado (imagen del Volume) y datos extraídos, con resaltado de la ubicación fuente de cada campo.
- [ ] Campos coloreados por confianza; edición inline con log (valor original, corrección, quién, motivo: error de extracción vs regla demasiado estricta).
- [ ] Flujo por lotes: cola priorizada, aprobar/rechazar/editar en masa, estadísticas de throughput del revisor.

### Fase 5 — Feedback y analítica (días 11–13)

- [ ] Correcciones → mejora del pipeline: nuevos few-shots con ejemplos corregidos, detección de errores sistemáticos, ajuste de umbrales de confianza con accuracy real.
- [ ] Dashboard: documentos/día, **tasa de auto-aprobación** (métrica estrella), accuracy por tipo y campo, tiempo medio de revisión.

### Fase 6 — Pulido (días 13–14)

- [ ] Demo ≤4 min: subir factura escaneada → extracción → validación pillando anomalía → corrección humana → analítica.
- [ ] Narrativa: "pipeline que auto-extrae con X% de accuracy, auto-aprueba el Y% sin tocar humano y reduce el tiempo manual un Z%".

## Criterios de éxito

- [ ] Factura correcta → auto-aprobada sin revisión.
- [ ] Factura con total que no cuadra → flag + cola de revisión con el campo resaltado.
- [ ] Cada corrección humana mejora el sistema (few-shots/umbrales).

## Datos y URLs

- Corpus: facturas/contratos sintéticos que generas tú (plantillas + datos ficticios). No uses documentos reales.
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → ai_parse_document; *Data engineering* → Auto Loader).

## ⚠️ Seguridad

- Documentos sintéticos únicamente: las facturas reales contienen datos fiscales.
- Las imágenes del Volume no se exportan al repo: solo el pipeline y plantillas de ejemplo.
