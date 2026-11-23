# Solución de referencia — cap-04 · Soporte al cliente gobernado

> ⚠️ Detalles en `ai-02`, `ai-07`, `ai-09`, `ai-12`. Aquí: el orden de las capas y por qué.

## El orden de las capas importa

```
request → [1 flag] → [2 caché] → [3 router] → modelo
```

1. **Flag primero**: la variante decide system prompt y modelo → determina la clave de caché y el tier del router. Si la caché fuera antes, servirías respuestas de la variante A a usuarios de la B.
2. **Caché antes del router**: un HIT evita pagar también el clasificador de complejidad.
3. **Router al final**: solo las peticiones que realmente necesitan LLM nuevo llegan aquí.

## Puntos de unión

### Clave de caché consciente de la variante

```python
variant = flag_client.evaluate("soporte_v2", ctx)
cache_key = sha256(variant.system_prompt + variant.model + str(variant.temperature))
# misma pregunta, distinta variante → distinta entrada de caché (correcto)
```

### Experimento sobre el router

```python
# el experimento no cambia el endpoint: cambia qué config resuelve el flag
exp = get_active_experiment("soporte_v2")
variant = assign_variant(user_id, exp)      # hash consistente (ai-09)
log_assignment(exp.id, user_id, variant)    # sin esto no hay análisis
```

### Guardián de calidad (rollback)

```python
if p10_last_100(flag, variant="experimental") < flag.quality_threshold:
    rollback(flag)      # pct=0, alerta, cooldown — ai-12
# los experimentos A/B y los flags comparten la misma tabla de métricas:
# una sola fuente de verdad para "¿esto va bien?"
```

### Informe de ahorro (la métrica que presentas)

```sql
SELECT sum(if(cache='HIT', coste_estimado, 0))            AS ahorro_cache,
       sum(coste_si_modelo_caro - coste_real)             AS ahorro_router,
       (ahorro_cache + ahorro_router) / sum(coste_si_modelo_caro) AS ahorro_total_pct
FROM cap_soporte.serving_log;
```

## Errores comunes

- Cachear antes del flag: contamina variantes entre sí (el error sutil del proyecto).
- Experimentar sobre métricas síncronas: el judge en el path del usuario mata la latencia.
- Rollback sin cooldown: oscilación on/off que degrada más que la variante mala.

## Cómo validarlo

1. 2.000 requests: ahorro total ≥50% documentado, P95 de HIT <50 ms.
2. Variante degradada: detectada en shadow o auto-rollback antes del 25%.
3. Ganador de experimento promocionado con p-value e IC visibles en el log.
