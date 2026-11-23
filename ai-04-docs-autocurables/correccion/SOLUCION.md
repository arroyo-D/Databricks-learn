# Solución de referencia — ai-04 · Documentación autocurable

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
PR abierto ──► GitHub Action
                 │
                 ├─ git diff → chunks de código afectados (parser)
                 ├─ lookup en docs_code_map (Delta) → secciones sospechosas
                 ├─ Job/notebook Databricks:
                 │     staleness check (ai_query) → rewrite (ai_query) → validación
                 └─ resultado JSON
                 │
        alta confianza ──► rama fix/docs-NNN + PR automático
        baja confianza ──► comentario en el PR con secciones flagged
```

## Código clave

### Embeddings + grafo en Vector Search

```python
# chunks: [(chunk_id, type, path, name, text)]
df = spark.createDataFrame(chunks, ["chunk_id","type","path","name","text"])
df.write.saveAsTable("main.ai_docs.chunks")

# Vector Search: índice Delta Sync con embeddings gestionados por Databricks
from databricks.vector_search.client import VectorSearchClient
vsc = VectorSearchClient()
index = vsc.create_delta_sync_index(
    endpoint_name="docs_vs",
    source_table_name="main.ai_docs.chunks",
    index_name="main.ai_docs.chunks_vs",
    pipeline_type="TRIGGERED",
    primary_key="chunk_id",
    embedding_source_column="text",
    embedding_model_endpoint_name="databricks-gte-large-en")
```

### Enlace por similitud

```python
for doc_section in doc_sections:
    hits = index.similarity_search(query_text=doc_section.text,
                                   columns=["chunk_id","text"], num_results=3)
    for h, score in hits:
        if score > UMBRAL_SIM:
            link(doc_section.id, h.chunk_id, score)
```

### Check de staleness

```python
STALE_PROMPT = """Código ANTES:\n{old}\n\nCódigo DESPUÉS:\n{new}\n\nDocumentación actual:\n{doc}\n\n¿Sigue siendo precisa la documentación? Responde JSON:
{{"stale": bool, "problems": [str], "confidence": "alta|media|baja"}}"""

verdict = json.loads(ai_query(MODEL, STALE_PROMPT.format(old=old, new=new, doc=section)))
```

### Rewrite dirigido

```python
FIX_PROMPT = """Reescribe SOLO las partes desactualizadas de esta sección.
Conserva estilo, tono y todo lo que siga siendo correcto.
Problemas detectados: {problems}\nCódigo nuevo:\n{new}\nSección actual:\n{doc}"""
```

### Action (extracto)

```yaml
on: {pull_request: {paths: ['src/**', 'docs/**']}}
steps:
  - uses: databricks/setup-cli@main
  - run: python scripts/doc_check.py --pr ${{ github.event.number }}
    env:
      DATABRICKS_TOKEN: ${{ secrets.DATABRICKS_TOKEN }}
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Decisiones clave

- **Dos pasadas (heurística + embeddings)**: la heurística da precisión, los embeddings recuerdo. El grafo guarda el origen de cada enlace.
- **Confianza → modo**: auto-PR solo con confianza alta y cambio acotado; el resto a comentario. Un bot que propone cambios malos pierde credibilidad en una semana.
- **Validación de la corrección** con segunda llamada LLM: la mitad de los drafts fallan aquí en V1, y mejor que fallen en CI que en el repo.

## Errores comunes

- Reescribir la sección entera → diffs enormes y estilo inconsistente. Reescribe fragmentos.
- No filtrar cambios de tests/comentarios → avalancha de falsos positivos.
- Umbral de similitud único: docs de config y de API tienen densidades distintas; hazlo por tipo.

## Cómo validarlo

1. Renombra una función documentada → la Action abre PR con el renombrado aplicado en docs.
2. Cambia solo un comentario → la Action informa "0 secciones afectadas".
3. En el fork de prueba, calcula precisión/recall sobre ≥20 cambios plantados y publícalo.
