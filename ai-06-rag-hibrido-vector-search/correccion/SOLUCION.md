# Solución de referencia — ai-06 · RAG híbrido con Vector Search

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
docs (Volume) → chunking x3 → rag_chunks (Delta) → Vector Search index (Delta Sync)
                                                        │
pregunta ─► retrieval denso top-10 ─┐                   │
          ─► keyword full-text ─────┼─► RRF ─► rerank ─► top-5
                                    │                   │
              grounded prompt + chunks numerados        │
                                    ▼                   │
              respuesta con [1][2] ─► verificador de citas
                                    │
              endpoint serving ◄────┴──── Agent Evaluation sobre golden set
```

## Código clave

### Índice híbrido

```python
from databricks.vector_search.client import VectorSearchClient
vsc = VectorSearchClient()
idx = vsc.create_delta_sync_index(
    endpoint_name="rag_vs",
    source_table_name="main.ai_rag.rag_chunks",
    index_name="main.ai_rag.rag_chunks_vs",
    pipeline_type="TRIGGERED",
    primary_key="chunk_id",
    embedding_source_column="text",
    embedding_model_endpoint_name="databricks-gte-large-en")
```

### Retrieval híbrido + RRF

```python
def hybrid(query, k_dense=10, k_kw=10, w_dense=0.7, w_kw=0.3):
    dense = idx.similarity_search(query_text=query, num_results=k_dense,
                                  columns=["chunk_id","text"])
    kw = idx.similarity_search(query_text=query, num_results=k_kw,
                               columns=["chunk_id","text"],
                               query_type="HYBRID")   # keyword+vector en Databricks
    return rrf_fuse([dense, kw], weights=[w_dense, w_kw])

def rrf_fuse(lists, weights, k=60):
    scores = {}
    for w, lst in zip(weights, lists):
        for rank, item in enumerate(lst):
            scores[item["chunk_id"]] = scores.get(item["chunk_id"], 0) + w / (k + rank + 1)
    return sorted(scores.items(), key=lambda kv: -kv[1])
```

### Prompt grounded + verificación de citas

```python
GROUNDED = """Responde SOLO con la información del contexto. Cita como [1], [2].
Si el contexto no basta, dilo explícitamente.\n\nContexto:\n{blocks}\n\nPregunta: {q}"""

def verify_citations(answer, chunks):
    for m in re.finditer(r"\[(\d+)\]", answer):
        claim = sentence_around(answer, m.start())
        chunk = chunks[int(m.group(1)) - 1]
        ok = judge(f"¿Este fragmento sostiene la afirmación?\nFragmento: {chunk}\nAfirmación: {claim}")
        if not ok: flag_unsupported(claim, m.group(1))
```

### Evaluación con MLflow

```python
import mlflow
with mlflow.start_run(run_name=f"eval_{strategy}"):
    mlflow.evaluate(
        model=lambda q: rag_pipeline(q),
        data=spark.table("main.ai_rag.rag_golden_qa").toPandas(),
        targets="golden_answer",
        extra_metrics=[faithfulness_metric, citation_accuracy_metric])
```

## Decisiones clave

- **Keyword importa en docs técnicas**: `DATABRICKS_TOKEN` o un código de error tienen señal semántica ~nula; sin full-text los pierdes.
- **Rerank solo al top-20**: rerankear todo es caro; rerankear nada deja precisión sobre la mesa.
- **Comparativa de chunking con la misma suite**: la decisión "semántico vs estructural" se toma con números, no con vibes.

## Errores comunes

- Chunk único gigante por documento: mata la precisión del retrieval.
- Confiar las citas sin verificar: los modelos citan "con formato perfecto" chunks que no dicen eso.
- Evaluar solo con preguntas fáciles: incluye multi-hop y sin-respuesta o el faithfulness será inflado.

## Cómo validarlo

1. Pregunta con un nombre exacto de función → híbrido lo recupera, solo-denso no (o peor).
2. Pregunta fuera del corpus → respuesta "no lo sé" con sugerencias.
3. Planta una cita falsa → el verificador la marca.
