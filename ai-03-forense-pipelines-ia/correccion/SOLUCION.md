# Solución de referencia — ai-03 · Forense de fallos en pipelines de IA

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
doc → [intake] → [extract] → [classify] → [summarize] → salida
        │          │            │              │
        └──────────┴─ @mlflow.trace spans ─────┘
                          │
                   traces (MLflow) → traces_flat (Delta)
                          │
         flag_bad_output(trace_id)
                          ▼
         backward analyzer: judge por span (de atrás hacia delante)
                          ▼
         failure_cases (Delta) ──► Job de re-eval ──► Dashboard
```

## Código clave

### Trazado por pasos

```python
import mlflow

@mlflow.trace(name="extract", span_type="LLM")
def extract(text: str) -> Extracted:
    out = ai_query(MODEL, EXTRACTION_PROMPT + text)
    conf = ai_query(MODEL, f"Confianza 1-5 en esta extracción: {out}")
    mlflow.update_current_trace(attributes={"step_confidence": conf})
    return Extracted.parse_raw(out)

@mlflow.trace(name="pipeline")
def run_pipeline(doc: str):
    return summarize(classify(extract(intake(doc))))
```

### Aplanar traces a Delta

```python
from mlflow.tracking import MlflowClient
traces = MlflowClient().search_traces(experiment_ids=[EXP_ID])
rows = [{"trace_id": t.info.request_id, "span": s.name, "status": s.status.status_code,
         "latency_ms": (s.end_time_ns - s.start_time_ns)/1e6,
         "confidence": s.attributes.get("step_confidence")}
        for t in traces for s in t.data.spans]
spark.createDataFrame(rows).write.mode("append").saveAsTable("main.ai_forensics.traces_flat")
```

### Analizador hacia atrás

```python
FAIL_TAXONOMY = ["extraction_hallucination", "misclassification",
                 "propagation_error", "prompt_failure", "context_loss"]

def backward_analyze(trace_id):
    spans = get_spans(trace_id)            # ordenados
    for span in reversed(spans):
        verdict = judge(f"Input:\n{span.inputs}\nOutput:\n{span.outputs}\n"
                        "¿Es el output una transformación razonable del input? "
                        "Responde JSON {ok: bool, failure_type, evidence}")
        if not verdict["ok"]:
            return RootCause(step=span.name, type=verdict["failure_type"],
                             evidence=verdict["evidence"])
```

### Caso de eval automático

```python
def on_human_confirm(flag, correction):
    spark.createDataFrame([{
        "case_id": flag.trace_id, "input": flag.original_input,
        "failing_step": flag.root_cause_step, "bad_output": flag.output,
        "corrected_output": correction, "failure_type": flag.root_cause_type
    }]).write.mode("append").saveAsTable("main.ai_forensics.failure_cases")
```

## Decisiones clave

- **Backward, no forward**: el error visible está al final; la causa casi siempre está antes. Empezar por el último span ahorra llamadas de judge.
- **Confidence por span**: los spans con confianza baja son sospechosos prioritarios → ordénalos antes de juzgar.
- **Judge con evidencia obligatoria**: sin cita del input/output, el diagnóstico no vale.

## Errores comunes

- Pasos espagueti: si `extract` y `classify` comparten estado, el trazado no aisla nada.
- Juzgar todos los spans siempre: carísimo; solo tras flag humano o score final bajo.
- No deduplicar `failure_cases`: el mismo fallo sistemático genera 50 casos idénticos.

## Cómo validarlo

1. Planta una factura multi-moneda → la salida final falla → el analyzer debe señalar `extract` (no `summarize`).
2. Confirma el fallo → aparece en `failure_cases` con taxonomía correcta.
3. Corrige el prompt de extracción → el Job de re-eval marca el caso como resuelto.
