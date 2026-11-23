# ai-07 · Caché semántica para APIs de LLM

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 7: Semantic Caching Layer for LLM APIs* de la guía BASWE.

**Nivel:** 🟡 Intermedio · **Duración estimada:** 10–12 días (2–3 h/día)
**Prerrequisitos:** `ai-02`, `dbx-10-databricks-sql`

## Qué vas a construir

Un proxy de caché entre cualquier aplicación y los LLMs: detecta peticiones **semánticamente equivalentes** ya respondidas ("¿Qué es Python?" ≈ "Explícame Python") y sirve la respuesta cacheada al instante. Objetivo: latencia ~0 en hits y 30–60% menos coste de API en cargas típicas.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| Redis + RedisVL | **Vector Search** (lookup por similitud) + tabla Delta `llm_cache` |
| text-embedding-3-small | Endpoint de embeddings gestionado (GTE) |
| FastAPI proxy | **Model Serving endpoint** (pyfunc) con contrato tipo OpenAI |
| Prometheus + Grafana | **AI/BI Dashboard** + métricas en Delta + SQL Alerts |
| Ollama local | Foundation Model APIs (pay-per-token) |

## Fases del proyecto

### Fase 1 — Índice y motor de similitud (días 1–3)

- [ ] Tabla `llm_cache`: cache_id, prompt_text, system_prompt_hash, model, temperature, response, tokens, created_at, ttl_expires_at, hit_count.
- [ ] Índice Vector Search sobre los embeddings del prompt (Delta Sync).
- [ ] Lookup: embebe el prompt entrante, busca el vecino más cercano; si coseno ≥ umbral (empieza en 0.95) → HIT; si no → MISS y forward al LLM.
- [ ] La clave de caché incluye hash del system prompt + modelo + parámetros de generación: dos prompts iguales con distinto system NO comparten entrada.

### Fase 2 — Proxy drop-in (días 3–5)

- [ ] Serving endpoint con el mismo contrato que `/chat/completions` de OpenAI: cualquier app cambia solo la base URL.
- [ ] Header extra `X-Cache: HIT|MISS` y campo `cache_similarity` en la respuesta.
- [ ] Soporte multi-modelo: la caché nunca sirve respuestas de un modelo a peticiones de otro.
- [ ] Solo cachea respuestas completas y correctas (finish_reason = stop; nunca errores ni cortes).

### Fase 3 — Políticas y evicción (días 5–8)

- [ ] TTL por tipo: factual/estable → 24h+; dependiente de tiempo/actualidad → 1h o no-cache. Clasificador simple que asigna el tier de TTL según el prompt.
- [ ] Invalidación: al cambiar el system prompt → invalida todas las entradas con ese hash; al actualizar modelo → invalida las suyas. Endpoint/SQL para invalidación manual por prefijo o tag.
- [ ] Tuner de umbral: notebook que re-juega datos históricos con umbrales 0.90/0.95/0.98 y muestra el tradeoff hit-rate vs respuestas-casi-correctas.
- [ ] Umbrales adaptativos por tipo de tarea: clasificación (tolera 0.90) vs generación creativa (0.98 o no-cache).

### Fase 4 — Monitorización (días 8–10)

- [ ] Todo a Delta: hits, misses, latencias, ahorro estimado por hora/día.
- [ ] AI/BI Dashboard: hit rate en tiempo real, ahorro acumulado (la métrica estrella), P50/P95/P99 hit vs miss, utilización de la caché, efecto del umbral en los últimos 7 días.
- [ ] Analizador de near-miss: queries que cayeron justo por debajo del umbral → candidatos a normalización (quitar muletillas antes de embeber) o a bajar el umbral.

### Fase 5 — Load test y documentación (días 10–12)

- [ ] 2.000+ peticiones con mezcla de únicas y repetidas (patrón realista). Mide: convergencia del hit rate, percentiles de latencia, ahorro total.
- [ ] README como propuesta de negocio: "esto es lo que ahorraría delante de nuestras llamadas LLM", con los números del load test.

### Fase 6 — Pulido (días 12–14)

- [ ] Demo: query nueva (latencia real) → misma query (instantánea) → query semánticamente similar (también hit) → dashboard acumulando ahorro.
- [ ] Titular: "caché drop-in que redujo coste un X% y latencia P95 un Y% en 2.000 peticiones".

## Criterios de éxito

- [ ] Una parafrasis da HIT con la respuesta correcta; una pregunta distinta da MISS.
- [ ] Cambiar el system prompt invalida las entradas asociadas.
- [ ] El dashboard muestra ahorro en € con query reproducible.

## Datos y URLs

- Prompts de carga: genera variantes tú mismo (parafrasis de 50 preguntas base).
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → Vector Search, Model Serving).

## ⚠️ Seguridad

- La caché guarda prompts y respuestas completos: solo datos sintéticos en este proyecto.
- No cachees respuestas con datos personales sin política de TTL e invalidación.
