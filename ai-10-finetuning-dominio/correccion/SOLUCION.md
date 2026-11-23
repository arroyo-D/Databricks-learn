# Solución de referencia — ai-10 · Fine-tuning en dominio

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
dataset curado (Delta: train/val/test, sin leakage)
        │
        ▼
Foundation Model Training run ──► MLflow run (hiperparámetros, curvas, checkpoints)
        │
        ▼
mejor checkpoint → registro en UC (main.ai_ft.mi_modelo, version N)
        │
        ├─► benchmark eval (base vs fine-tuned, mismo set, mismos criterios)
        │        └─► judge ciego + test de forgetting
        ▼
Model Serving endpoint + comparador A/B
```

## Código clave

### Formato del dataset

```python
# instruction-following format
{"instruction": "Clasifica la cláusula en una de: [terminacion, pago, confidencialidad, responsabilidad]",
 "input": "El presente contrato podrá resolverse por cualquiera de las partes con preaviso de 30 días.",
 "output": "terminacion"}
```

### Lanzar fine-tuning (API de Databricks)

```python
from databricks.model_training import foundation_model as fm
run = fm.create(
    model="meta-llama/Meta-Llama-3.1-8B-Instruct",
    train_data_path="dbfs:/Volumes/main/ai_ft/data/train.jsonl",
    eval_data_path="dbfs:/Volumes/main/ai_ft/data/val.jsonl",
    register_to="main.ai_ft",
    training_duration="3ep",
    learning_rate="2e-5")
print(run.name)  # trackeado en MLflow automáticamente
```

### Evaluación pareada base vs fine-tuned

```python
results = []
for ex in benchmark:
    base_out = query(base_endpoint, ex.prompt)
    ft_out = query(ft_endpoint, ex.prompt)
    verdict = judge_blind(ex.prompt, base_out, ft_out)  # no sabe cuál es cuál
    results.append({"id": ex.id, "base": base_out, "ft": ft_out,
                    "winner": verdict.winner, "reason": verdict.reason})
spark.createDataFrame(results).write.saveAsTable("main.ai_ft.head_to_head")
```

### Forgetting check

```python
general = load_general_qa_subset()  # 50 preguntas de conocimiento general
for name, ep in [("base", base_endpoint), ("ft", ft_endpoint)]:
    acc = accuracy(query_many(ep, general))
    log_metric(f"general_accuracy_{name}", acc)
```

## Decisiones clave

- **Tarea estrecha**: "mejorar el modelo en general" no es medible; "clasificar cláusulas legales" sí.
- **Judge ciego**: si el juez sabe cuál es el fine-tuned, el sesgo es enorme y sutil.
- **Regresiones documentadas**: un informe honesto con casos donde empeoró vale más en una entrevista que un "mejoró un 20%" sin matices.

## Errores comunes

- Leakage entre splits (mismo documento fuente en train y test) → métricas infladas que no se reproducen.
- Evaluar solo con el test split del entrenamiento: hazlo con benchmark manual independiente.
- Fine-tuning cuando bastaba few-shot: demuestra primero que el baseline con buen prompting NO resuelve la tarea.

## Cómo validarlo

1. El baseline (modelo base + buen prompt) está medido ANTES de entrenar.
2. Head-to-head muestra mejora agregada Y casos de regresión identificados.
3. `general_accuracy_ft / general_accuracy_base ≥ 0.9` (o el tradeoff documentado).
