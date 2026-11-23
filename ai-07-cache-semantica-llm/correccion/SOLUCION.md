# Solución de referencia — ai-07 · Caché semántica para LLMs

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
app ──► Serving endpoint (contrato OpenAI)
            │
            ├─ key = hash(system_prompt + model + params)
            ├─ embed(prompt) → Vector Search nearest neighbor (filtro por key)
            ├─ sim ≥ umbral → HIT: response cacheada (+hit_count) ──► cliente
            └─ MISS → LLM real → respuesta completa
                        │
                 INSERT llm_cache (prompt, embedding, response, ttl)
                        │
        métricas Delta → AI/BI Dashboard (ahorro €, hit rate, latencias)
```

## Código clave

### Tabla + lookup

```sql
CREATE TABLE main.ai_cache.llm_cache (
  cache_id STRING, prompt_text STRING, prompt_embedding ARRAY<FLOAT>,
  system_prompt_hash STRING, model STRING, temperature DOUBLE,
  response STRING, tokens_out INT, created_at TIMESTAMP,
  ttl_expires_at TIMESTAMP, hit_count BIGINT);
```

```python
def cached_completion(req):
    key = sha256(req.system + req.model + str(req.temperature))
    hits = vs_idx.similarity_search(
        query_text=req.prompt, num_results=1,
        filters={"system_prompt_hash": key, "model": req.model},
        columns=["response","ttl_expires_at"])
    if hits and hits[0]["score"] >= THRESH and not expired(hits[0]):
        increment_hit_count(hits[0])
        return hits[0]["response"], "HIT", hits[0]["score"]
    resp = call_llm(req)
    if resp.finish_reason == "stop":
        insert_cache(req, key, resp, ttl=classify_ttl(req.prompt))
    return resp.text, "MISS", None
```

### Query de ahorro

```sql
SELECT date_trunc('hour', ts) AS hora,
       count_if(cache = 'HIT') * 1.0 / count(*) AS hit_rate,
       sum(if(cache='HIT', est_cost, 0)) AS ahorro_estimado
FROM main.ai_cache.cache_metrics GROUP BY 1 ORDER BY 1;
```

### Tuner de umbral

```python
for thr in [0.90, 0.93, 0.95, 0.98]:
    replay = replay_history(threshold=thr)
    report(thr, hit_rate=replay.hit_rate, wrong_rate=replay.wrong_hit_rate)
```

## Decisiones clave

- **Filtro por key ANTES de rankear**: similitud alta entre dos system prompts distintos es contaminación, no hit.
- **TTL clasificado, no global**: cachear "¿quién ganó ayer?" 24h es un bug funcional.
- **Near-miss como fuente de tuning**: los casos justo bajo el umbral son el dataset perfecto para decidir si bajarlo.

## Errores comunes

- Cachear por texto exacto: no es caché semántica, es un dict.
- Cachear respuestas con finish_reason=length: sirves respuestas cortadas para siempre.
- Un solo umbral global: clasificación y redacción creativa tienen tolerancias opuestas.

## Cómo validarlo

1. 50 parafrasis de la misma pregunta → hit rate ≥ 80% con 0 respuestas erróneas.
2. Cambia el system prompt → las mismas preguntas dan MISS.
3. Load test de 2.000 requests → ahorro positivo y P95 de HIT < 50 ms.
