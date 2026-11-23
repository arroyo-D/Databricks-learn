# Solución de referencia — ai-12 · Feature flags para IA

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
flag_config (Delta: pct, thresholds, baseline, experimental)
      │
app → flag_client.evaluate(flag, user_ctx) → variante (hash consistente)
      │
      ├─ serve baseline o experimental → usuario
      └─ log de cada servicio → flag_serving_log (Delta)
                                        │
              Streaming job: judge (ai_query) → flag_quality_log
                                        │
              Monitor: ventanas rodantes + canary stats
                    ├─ calidad ok → avanza etapa (1→5→25→50→100%)
                    └─ P10 < umbral sostenido → ROLLBACK (pct=0, alerta)
```

## Código clave

### SDK con degradación elegante

```python
class FlagClient:
    def __init__(self): self._cache, self._ts = {}, 0
    def evaluate(self, flag, user_ctx):
        try:
            cfg = self._config(flag)          # cache TTL 60s
        except Exception:
            return self._cache.get(flag, {}).get("baseline")  # servicio caído → baseline
        if not in_targeting(cfg, user_ctx): return cfg.baseline
        bucket = hash(f"{flag}:{user_ctx['id']}") % 100
        return cfg.experimental if bucket < cfg.rollout_pct else cfg.baseline
```

### Ventana rodante y trigger de rollback

```sql
WITH ultimas AS (
  SELECT flag, score, variant,
         row_number() OVER (PARTITION BY flag, variant ORDER BY ts DESC) AS rn
  FROM main.ai_flags.flag_quality_log)
SELECT flag, variant,
       percentile(score, 0.10) AS p10, avg(score) AS media, count(*) AS n
FROM ultimas WHERE rn <= 100 GROUP BY 1,2;
```

```python
def rollback_trigger(flag):
    w = last_window(flag, variant="experimental", n=100)
    if w.p10 < flag.quality_threshold and consecutive_bad(flag) >= 50:
        set_rollout(flag, 0); alert(flag, w); log_event(flag, "auto_rollback"); cooldown(flag)
```

### Canary entre etapas

```python
def can_advance(flag):
    exp, base = scores(flag, "experimental"), scores(flag, "baseline")
    res = stats.mannwhitneyu(exp, base, alternative="less")  # ¿exp peor?
    return res.pvalue > 0.05   # solo avanza si NO es significativamente peor
```

### Modo shadow

```python
if cfg.shadow:
    shadow_out = serve(cfg.experimental, req)
    log_shadow(flag, req, shadow_out)   # evaluación offline; el usuario ve baseline
    return serve(cfg.baseline, req)
```

## Decisiones clave

- **P10, no media**: una media de 4.5 puede esconder un 10% de respuestas desastrosas; el P10 es lo que el usuario desafortunado ve.
- **Cooldown anti-flapping**: tras un rollback, el flag no puede reactivarse solo durante X horas.
- **Baseline siempre disponible**: si el servicio de flags muere, la app sirve baseline (la feature no desaparece).

## Errores comunes

- Evaluar calidad en el path del usuario: +300ms de judge por request.
- Rollout por porcentaje sin hash consistente: el mismo usuario ve comportamientos distintos entre requests.
- Rollback solo por error rate: en IA el error típico es calidad mala con HTTP 200.

## Cómo validarlo

1. Variante experimental con prompt roto en shadow mode → detectado, 0 usuarios afectados.
2. La misma variante al 5% → rollback automático + alerta + evento en el log.
3. Variante buena → avanza por etapas hasta 100% con los canary checks en verde.
