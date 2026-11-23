# Solución de referencia — cap-02 · Knowledge Assistant

> ⚠️ Los detalles de cada pieza están en `ai-06`, `ai-01` y `ai-13`. Aquí: cómo se unen.

## Los tres bucles que hacen el sistema

```
Bucle 1 (servir):   pregunta → retrieval híbrido → generación citada → usuario
Bucle 2 (evaluar):  cambio de config → suite golden en CI → ¿regresión? → bloqueo
Bucle 3 (aprender): tráfico real → inference table → auto-etiquetado → golden set crece
```

La clave del proyecto es que **los tres bucles comparten las mismas tablas**: el golden set del bucle 2 es el destino del bucle 3, y la configuración del bucle 1 es lo que el bucle 2 versiona.

## Puntos de unión con código

### Config versionada (el artefacto del CI)

```yaml
# prompts/knowledge_assistant.yaml  (Git + Volume)
version: 7
retrieval: {k_dense: 10, k_keyword: 10, w_dense: 0.7, rerank_top: 5}
chunking: {strategy: structural, max_chars: 1200, overlap: 150}
generation: {model: databricks-meta-llama-3-3-70b-instruct, temperature: 0.1}
confidence: {retrieval_threshold: 0.35}
```

El CI evalúa el YAML del PR, no lo que haya desplegado: reproducibilidad total.

### Auto-crecimiento del golden set (extracto del job nocturno)

```python
logs = spark.table("cap_assistant.inference_payloads")
candidatos = logs.where("confidence < 0.5 OR feedback = 'down'")
for row in candidatos.collect():
    if not is_duplicate(row.question, golden_set):   # coseno < 0.92
        label = auto_label(row)                       # ai-13
        add_to_golden(row, label) if label.confident else review_queue.push(row)
```

### Métrica que cuenta la historia

```sql
SELECT week, casos_golden, faithfulness_promedio, citation_accuracy
FROM cap_assistant.weekly_metrics ORDER BY 1;
-- la curva de casos_golden subiendo mientras faithfulness se mantiene = sistema sano
```

## Errores comunes

- Evaluar solo al principio: sin CI de comportamiento, cada "pequeño cambio de prompt" degrada silenciosamente.
- Auto-añadir preguntas triviales al golden set: el filtro de dificultad/interés es lo que mantiene el valor del dataset.
- Mezclar PII de usuarios en el golden set: anonimiza en el job, no después.

## Cómo validarlo

1. CI: PR con prompt degradado → job rojo con diff de métricas.
2. Flywheel: pregunta fallida hoy → en el golden set mañana → evaluada en el próximo run.
3. Dashboard semanal muestra crecimiento del dataset y estabilidad de faithfulness.
