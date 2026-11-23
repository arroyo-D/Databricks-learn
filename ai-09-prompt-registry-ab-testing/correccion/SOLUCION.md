# Solución de referencia — ai-09 · Registro de prompts y A/B testing

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
MLflow Prompt Registry (UC)
   prompt://email_classifier/3  ← versiones + alias 'production'
              │
Serving endpoint (pyfunc)
   1. resolve: ¿experimento activo? → splitter hash(user_id) → variante
   2. fill template {{vars}} + validación
   3. LLM call → log (variant, latencia, tokens, coste)
              │
   async Job: métricas custom (judge, accuracy) → experiment_metrics (Delta)
              │
   stats engine: t-test/Mann-Whitney → significancia → auto-promote
              │
   AI/BI Dashboard + audit log (prompt_audit)
```

## Código clave

### Registry con alias

```python
from mlflow import MlflowClient
c = MlflowClient(registry_uri="databricks-uc")
c.register_prompt(name="main.ai_exp.email_classifier",
                  template="Clasifica el email... {{email_text}}")
c.set_prompt_alias("main.ai_exp.email_classifier", "production", version=3)
```

### Splitter consistente

```python
def assign_variant(user_id: str, experiment) -> str:
    h = int(hashlib.md5(f"{experiment.id}:{user_id}".encode()).hexdigest(), 16) % 100
    acc = 0
    for variant, pct in experiment.traffic_split.items():
        acc += pct
        if h < acc: return variant
    return experiment.control
```

### Significancia

```python
from scipy import stats

def evaluate_experiment(exp_id):
    m = spark.table("main.ai_exp.experiment_metrics").where(f"exp_id='{exp_id}'").toPandas()
    a, b = m[m.variant=='A'].score, m[m.variant=='B'].score
    t, p = stats.ttest_ind(a, b, equal_var=False)   # Welch
    ci = stats.t.interval(0.95, len(a)+len(b)-2,
                          loc=b.mean()-a.mean(),
                          scale=((a.var()/len(a)+b.var()/len(b))**0.5))
    return {"p_value": p, "ci_95": ci, "significant": p < 0.05}
```

### Guardrail de experimento

```python
if error_rate(variant, window="1h") > 0.10 or quality_gap_significant():
    stop_experiment(exp_id); notify_owner(); rollback_to_control()
```

## Decisiones clave

- **Hash con experiment.id**: reutilizar solo user_id haría que un usuario cayera siempre en la misma "posición" en todos los experimentos (sesgo).
- **Welch, no t-test clásico**: varianzas por variante raramente iguales.
- **Hold de 24h antes de auto-promote**: la significancia estadística no te salva de un bug de logging.

## Errores comunes

- Mirar el experimento cada hora y parar "cuando da significativo" → p-hacking. Define n objetivo o usa corrección por peeking.
- Métrica custom en el path del usuario: la llamada al judge va asíncrona siempre.
- No loguear la variante en cada request: sin eso no hay análisis posible.

## Cómo validarlo

1. 1.000 requests con 3 variantes → cada user_id siempre en la misma.
2. Variante B degradada a propósito → auto-stop antes del n objetivo.
3. Simula dos distribuciones conocidas (misma media) → el test dice "inconcluso", no "ganador".
