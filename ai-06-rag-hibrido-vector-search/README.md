# ai-06 · RAG híbrido sobre documentación interna (Vector Search)

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 6: RAG Pipeline with Hybrid Search* de la guía BASWE.

**Nivel:** 🔴 Avanzado · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `dbx-06-auto-loader`, `dbx-07-delta-lake`, `ai-01`

## Qué vas a construir

Un sistema RAG de nivel producción: ingesta documentación (Markdown, HTML, PDF, texto), la trocea con estrategias configurables, la indexa con **búsqueda híbrida (vectorial + keyword)**, recupera el contexto relevante y genera respuestas con **citaciones verificadas**. Con suite de evaluación que compara estrategias de chunking con números.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| ChromaDB + BM25 (rank_bm25) | **Vector Search** con índice híbrido (keyword + semántico) en un solo servicio |
| text-embedding-3-small | Embeddings gestionados por Databricks (GTE/BGE) en el índice Delta Sync |
| Cross-encoder reranker | Rerank con `ai_query` (LLM-as-reranker) o endpoint de reranking |
| FastAPI + Streamlit | **Model Serving** + AI/BI dashboard o Databricks App |
| Golden Q&A en JSON | Tabla Delta `rag_golden_qa` |
| Eval con RAGAS custom | **MLflow Evaluate / Agent Evaluation** (faithfulness, relevance, correctness) |

## Fases del proyecto

### Fase 1 — Ingesta y chunking (días 1–3)

- [ ] Loader multi-formato (md, txt, html, pdf) → texto limpio + metadata (fuente, sección, página). Raw en Volume, procesado en Delta.
- [ ] 3 estrategias de chunking intercambiables: tamaño fijo con solape (baseline), por estructura (encabezados), semántico (corte por similitud de embeddings).
- [ ] Tabla `rag_chunks`: chunk_id, doc, sección, estrategia, n_chars, texto.
- [ ] Deduplicación: skip si similitud coseno > 0.95 con un chunk existente.
- [ ] Índice Vector Search Delta Sync sobre `rag_chunks` (embeddings gestionados).

### Fase 2 — Recuperación híbrida (días 3–6)

- [ ] Retrieval denso: similarity search top-10.
- [ ] Retrieval keyword: usa la búsqueda híbrida del índice (full-text) para captar nombres de función, claves de config y códigos de error exactos.
- [ ] Fusión RRF (Reciprocal Rank Fusion) con pesos configurables (0.7 denso / 0.3 keyword para empezar).
- [ ] Rerank: top-20 fusionados → reranker (LLM-as-judge de relevancia) → top-5 final.

### Fase 3 — Generación con citas verificadas (días 6–9)

- [ ] Prompt grounded: responde SOLO desde el contexto, cita con [1] [2], di explícitamente si el contexto no basta.
- [ ] Verificación de citas: cada par (claim, cita) pasa por un judge que confirma que el chunk citado sostiene la afirmación. Flags para citas no soportadas.
- [ ] Score de confianza compuesto: relevancia del retrieval, cobertura de citas verificadas, completitud.
- [ ] "No lo sé" elegante: si la confianza de retrieval < umbral, responde qué encontró, qué no, y qué documentos revisar manualmente.

### Fase 4 — Framework de evaluación (días 9–11)

- [ ] Golden set de 50+ pares pregunta-respuesta escritos a mano sobre tu corpus: lookups directos, multi-hop, sin respuesta en el corpus, ambiguas.
- [ ] Eval automatizada con MLflow: correctness (vs golden), faithfulness (grounding), retrieval relevance, citation accuracy.
- [ ] Comparativa de las 3 estrategias de chunking con la misma suite → tabla de resultados que justifica tu elección.

### Fase 5 — API y dashboard (días 11–13)

- [ ] Serving endpoint `ask`: pregunta → respuesta + citas + scores + metadata de fuentes.
- [ ] UI (App/dashboard): respuesta con citas clicables, chunks recuperados ordenados, toggle híbrido vs solo-denso lado a lado.
- [ ] Job de ingesta: nuevo documento al Volume → re-chunk + sync del índice.

### Fase 6 — Pulido (días 13–14)

- [ ] Demo ≤4 min: ingestar docs, preguntas de dificultad creciente, la verificación de citas pillando una alucinación, comparativa híbrido vs denso.
- [ ] Case study con números: "X% faithfulness, Y% citation accuracy en suite de 50 preguntas".

## Criterios de éxito

- [ ] Híbrido supera a solo-denso en preguntas con términos exactos (con datos).
- [ ] Pregunta sin respuesta en el corpus → "no lo sé" en vez de alucinación.
- [ ] Cita no soportada detectada por el verificador.

## Datos y URLs

- Corpus: tu propia documentación (¡los READMEs de este repo valen!), o documentación pública descargada de proyectos open source.
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → Vector Search, Agent Evaluation).

## ⚠️ Seguridad

- Si usas documentación interna real de una empresa, no la subas al repo: solo el pipeline.
- El golden set se escribe a mano: no lo generes con el mismo LLM que evalúas.
