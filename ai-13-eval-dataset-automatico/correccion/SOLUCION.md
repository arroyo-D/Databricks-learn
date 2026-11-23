# Solución de referencia — ai-13 · Generador de eval datasets desde logs

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
inference tables (Delta, de tus serving endpoints)
      │
      ▼
normalización + PII redaction + muestreo estratificado
      │
      ▼
embeddings → clustering (categorías) + outliers (edge cases)
      │
      ▼
judge: calidad 1-5 + dificultad + comportamiento esperado
      │
      ├─ confianza alta ──► auto-add a eval_dataset (Delta)
      └─ confianza baja ──► review_queue → humano decide
      │
      ▼
Job nocturno (Lakeflow) → dataset crece → eval runner → regresiones
```

## Código clave

### Muestreo potenciado por señal

```python
def sample(logs, n=200):
    hard = logs.where("feedback = 'down' OR retried = true OR latency_ms > 8000")
    easy = logs.subtract(hard)
    return hard.sample(0.5).union(easy.sample(False, max(0, n - hard.count()) / easy.count()))
```

### Clustering de prompts

```python
from sklearn.cluster import HDBSCAN
emb = np.array([embed(p) for p in prompts])
labels = HDBSCAN(min_cluster_size=8).fit_predict(emb)
# label = -1 → outlier → candidato premium a caso de eval
```

### Auto-etiquetado con confianza

```python
def auto_label(case, runs=3):
    labels = [label_once(case) for _ in range(runs)]   # misma llamada, T>0
    if agreement(labels) >= 2/3:
        return majority(labels), "auto"
    return labels[0], "review"   # a la cola humana
```

### Dedupe + cobertura

```sql
SELECT cluster_id, count(*) AS casos,
       count(*) * 1.0 / sum(count(*)) OVER () AS cobertura
FROM main.ai_evalgen.eval_dataset GROUP BY 1 ORDER BY 3;
-- clusters con cobertura < 5% → prioridad en la próxima generación
```

### Job nocturno (databricks.yml extracto)

```yaml
resources:
  jobs:
    eval_dataset_growth:
      schedule: {quartz_cron_expression: "0 0 3 * * ?", timezone_id: "Europe/Madrid"}
      tasks:
        - {task_key: sample, notebook_task: {notebook_path: ./nb/sample}}
        - {task_key: label, depends_on: [{task_key: sample}], notebook_task: {notebook_path: ./nb/label}}
        - {task_key: dedupe_add, depends_on: [{task_key: label}], notebook_task: {notebook_path: ./nb/dedupe}}
```

## Decisiones clave

- **Outliers > casos medios**: un caso más de la categoría mayoritaria aporta poco; un prompt que no encaja en ningún cluster es oro.
- **Rúbrica para subjetivo**: una "golden answer" única para una pregunta subjetiva convierte el eval en lotería; la rúbrica aguanta.
- **Confianza por acuerdo de N pasadas**: más barato que un humano, más honesto que una sola llamada.

## Errores comunes

- Auto-etiquetar todo: la cola de revisión humana es lo que mantiene la calidad del dataset.
- No deduplicar: el dataset "crece" pero la cobertura real se estanca.
- Medir el crecimiento en nº de casos, no en cobertura: 1.000 casos idénticos no evalúan nada nuevo.

## Cómo validarlo

1. Tras una semana de tráfico sintético: dataset ≥ X casos con cobertura razonable por cluster.
2. Degrada un prompt en producción → el job genera casos negativos → el eval runner los detecta.
3. Revisa 20 auto-etiquetas a mano: precisión ≥ 85% o sube el umbral de confianza.
