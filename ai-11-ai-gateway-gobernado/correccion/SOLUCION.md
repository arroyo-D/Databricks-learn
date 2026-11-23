# Solución de referencia — ai-11 · Gateway de LLMs gobernado

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
equipos (service principals)
      │
      ▼
Router pyfunc (serving endpoint)
      ├─ authn: quién es → config de equipo (tabla Delta)
      ├─ rate limit + presupuesto (AI Gateway nativo + job guardián)
      ├─ retry ×3 backoff → primario
      │      ✗ retryable → fallback por tier (OpenAI → Anthropic → Llama local)
      └─ circuit breaker por proveedor
      │
      ▼
AI Gateway: usage tracking + inference tables → Delta
      │
Dashboards (ops / negocio / rendimiento) + SQL Alerts
```

## Código clave

### Retry + fallback con circuit breaker

```python
class CircuitBreaker:
    def __init__(self, fail_threshold=5, window_s=60, cooldown_s=120):
        self.state = "closed"; self.fails = deque(); self.opened_at = None
    def allow(self):
        if self.state == "open":
            if time.time() - self.opened_at > self.cooldown_s:
                self.state = "half-open"; return True
            return False
        return True
    def report(self, ok):
        now = time.time()
        if ok: self.state = "closed"; self.fails.clear(); return
        self.fails.append(now)
        while self.fails and now - self.fails[0] > self.window_s: self.fails.popleft()
        if len(self.fails) >= self.fail_threshold:
            self.state = "open"; self.opened_at = now; log_state_change("open")

RETRYABLE = (429, 500, 502, 503, 504)

def route(req):
    for provider in FALLBACK_CHAIN[req.tier]:
        cb = breakers[provider]
        if not cb.allow(): continue
        for attempt in range(3):
            try:
                resp = call(provider, req); cb.report(True); return resp
            except ProviderError as e:
                if e.status not in RETRYABLE: raise
                cb.report(False); time.sleep(2 ** attempt)
    raise AllProvidersDown()
```

### Presupuesto por equipo (job guardián)

```sql
WITH gasto AS (
  SELECT custom_tags['team'] AS equipo,
         sum(usage_quantity * pricing.default) AS gasto_hoy
  FROM system.billing.usage
  WHERE usage_date = current_date() AND sku_name LIKE '%FOUNDATION_MODEL%'
  GROUP BY 1)
SELECT g.equipo, g.gasto_hoy, p.presupuesto_diario,
       g.gasto_hoy / p.presupuesto_diario AS pct
FROM gasto g JOIN main.ai_gw.presupuestos p USING (equipo);
```

### Health check

```python
def health_loop():
    for ep in endpoints:
        t0 = time.perf_counter()
        ok = ping(ep)
        record(ep, ok, time.perf_counter() - t0)
```

## Decisiones clave

- **Fallback por tier, no por modelo**: si mañana cambias el primario, la cadena sigue funcionando.
- **AI Gateway nativo primero, custom después**: rate limits y logging ya existen; tu código solo cubre lo que falta (presupuestos por equipo, circuit breaker).
- **Inference tables como fuente de verdad**: los dashboards no consultan al router en vivo, consultan Delta (desacoplado y auditable).

## Errores comunes

- Retry ante 401/403: jamás se arregla reintentando; solo quema cuota.
- Circuit breaker sin half-open: un proveedor recuperado queda vetado para siempre.
- Medir el presupuesto leyendo la tabla de logs en vez de `system.billing.usage`: el billing oficial manda.

## Cómo validarlo

1. Mock del primario devolviendo 503 → el cliente recibe respuesta del fallback sin notarlo.
2. Equipo con presupuesto de 0,01 € → bloqueado tras las primeras requests, alerta emitida.
3. Abre el circuito con 5 fallos → tráfico 100% al fallback; recupera → half-open → closed, todo logueado.
