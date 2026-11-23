# Solución de referencia — cap-03 · Fábrica de extracción

> ⚠️ Detalles en `ai-14` y `ai-03`. Aquí: la integración industrial.

## Lo que hace "fábrica" a un pipeline de extracción

Un script que extrae campos de un PDF es un demo. Una fábrica tiene: ingesta continua, enrutado por confianza, revisión humana acotada, diagnóstico automático de fallos y mejora con cada corrección.

## Puntos de unión

### Confianza por campo → decisión por documento

```python
def route(doc_extraction):
    field_confs = {f: field_confidence(f, v, doc.text) for f, v in doc_extraction}
    doc_conf = min(field_confs.values())          # el eslabón más débil manda
    if doc_conf >= 0.85 and rules_pass(doc_extraction):
        return "auto_approve"
    if doc_conf >= 0.6:
        return "quick_review"      # solo los campos en rojo
    return "full_review"
```

### Forense integrado (cuando el humano dice "esto está mal")

```python
def on_reject(doc_id, trace_id):
    cause = backward_analyze(trace_id)        # ai-03
    log_failure(doc_id, cause)
    if cause.type == "extraction_hallucination":
        tighten_prompt(cause.step, evidence=cause.evidence)
    create_eval_case(doc_id, cause)           # el fallo se convierte en test
```

### Métrica estrella

```sql
SELECT date_trunc('week', processed_at) AS semana,
       count_if(route = 'auto_approve') * 1.0 / count(*) AS auto_aprobacion,
       avg(review_seconds) FILTER (WHERE route <> 'auto_approve') AS t_medio_revision
FROM cap_docs.processing_log GROUP BY 1 ORDER BY 1;
-- objetivo: auto_aprobacion ↑ semana a semana gracias al feedback loop
```

## Errores comunes

- Enrutar por confianza media del documento: esconde el campo malo entre nueve buenos. Usa el mínimo o revisión por campo.
- Corregir sin registrar el motivo: "error de extracción" y "regla demasiado estricta" arreglan cosas distintas.
- No re-medir tras cada corrección: el dashboard de auto-aprobación es tu prueba de mejora.

## Cómo validarlo

1. 20 documentos limpios → auto-aprobación ≥80%, 0 errores en gold.
2. Documento con fecha manipulada (32/13) → validación lo para; el analyzer señala el span correcto.
3. Misma clase de error dos veces → tras la segunda corrección, el tercer documento similar pasa limpio.
