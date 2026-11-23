# Solución de referencia — ai-14 · Procesador de documentos

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
Volume (dropzone) ──► Auto Loader ──► documents_raw
      │
      ▼
ai_parse_document (o endpoint visión si imagen/escaneado difícil)
      │
      ▼
clasificador de tipo → esquema Pydantic → ai_query (JSON schema)
      │
      ▼
validación tipos + reglas negocio + anomalías → confianza por campo
      │
      ├─ alta ──► auto-aprobado ──► documents_gold
      └─ media/baja ──► review_queue ──► humano ──► correcciones ──► feedback loop
                                          │
                          dashboard: auto-approval rate, accuracy por campo
```

## Código clave

### Extracción de texto

```sql
SELECT path,
  ai_parse_document(content) AS parsed
FROM main.ai_docs.documents_raw
WHERE path LIKE '%.pdf';
```

### Extracción estructurada con esquema

```python
INVOICE_SCHEMA = {
  "type": "json_schema",
  "json_schema": {"name": "invoice", "schema": Invoice.model_json_schema(), "strict": True}}

raw = ai_query(MODEL,
    f"Extrae SOLO lo presente en el texto. Sin inferencias.\n{texto}",
    response_format=INVOICE_SCHEMA)
invoice = Invoice.model_validate_json(raw)
```

### Confianza por campo

```python
def field_confidence(field, value, source_text, chunks_agree):
    score = 0.0
    if str(value) in source_text: score += 0.5          # match exacto
    elif fuzzy_match(value, source_text) > 0.8: score += 0.3
    if chunks_agree: score += 0.3
    if passes_format_validation(field, value): score += 0.2
    return score
```

### Validación cruzada (reglas)

```python
@field_validator("total")
def total_cuadra(cls, v, info):
    lineas = info.data.get("line_items", [])
    if lineas and abs(sum(l.total for l in lineas) - v) > 0.02:
        raise ValueError("las líneas no suman al total")
    return v
```

### Registro de correcciones

```python
def on_human_edit(doc_id, field, old, new, reviewer, motivo):
    log_correction(doc_id, field, old, new, reviewer, motivo)
    if motivo == "extraction_error":
        add_few_shot_example(doc_id, field, new)   # mejora futura extracción
    elif motivo == "rule_too_strict":
        flag_rule_review(field)
```

## Decisiones clave

- **Confianza por campo, no por documento**: una factura puede tener 9 campos perfectos y 1 dudoso; enrutar todo el documento a revisión desperdicia el 90% del trabajo.
- **"Solo lo presente en el texto"** en el prompt: el 80% de las alucinaciones de extracción nacen de dejar al modelo "inferir valores razonables".
- **Las correcciones humanas son el dataset**: cada edición es un few-shot futuro o una regla a relajar.

## Errores comunes

- Un único pipeline para todos los tipos: sin clasificador previo, la extracción mezcla esquemas.
- Merge de chunks sin tracking de fuente: cuando dos chunks discrepan no sabes de dónde vino cada valor.
- Auto-aprobación sin histórico: fija umbrales con accuracy medida, no con intuición.

## Cómo validarlo

1. 20 facturas correctas → ≥80% auto-aprobadas con 0 errores.
2. Factura con total manipulado → flag de validación cruzada + cola de revisión.
3. Corrige un campo dos veces con el mismo error → el tercer documento similar ya lo extrae bien (few-shot añadido).
