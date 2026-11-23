# Solución de referencia — ai-02 · Enrutador de costes LLM

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
cliente ──► Model Serving endpoint (pyfunc router)
                 │
                 ├─ 1. features del prompt
                 ├─ 2. clasificador MLflow (tier 1/2/3)
                 ├─ 3. mapa tier→modelo (tabla Delta, cache TTL)
                 ├─ 4. llamada al modelo elegido (serving/FMAPI)
                 └─ 5. INSERT async en routing_log + verification_queue
                                    │
              Lakeflow Job verificador: re-ejecuta con modelo alto,
              compara, marca routing_failure, re-etiqueta
                                    │
              AI/BI Dashboard: ahorro €, distribución, escalaciones
```

## Código clave

### Registro de modelos

```sql
CREATE TABLE main.ai_router.model_registry (
  model_id STRING, endpoint STRING, cost_per_1k_input DOUBLE,
  cost_per_1k_output DOUBLE, avg_latency_ms INT, quality_tier STRING);
```

### Pyfunc router (esqueleto)

```python
import mlflow.pyfunc, hashlib, time, requests

class CostRouter(mlflow.pyfunc.PythonModel):
    def load_context(self, context):
        self.clf = mlflow.pyfunc.load_model(context.artifacts["classifier"])
        self.route_map = {"T1": "databricks-meta-llama-3-1-8b-instruct",
                          "T2": "databricks-meta-llama-3-3-70b-instruct",
                          "T3": "databricks-claude-sonnet-4"}  # leer de Delta en real
    def predict(self, context, df):
        outs = []
        for prompt in df["prompt"]:
            tier = self.clf.predict([self._features(prompt)])[0]
            model = self.route_map[tier]
            t0 = time.perf_counter()
            resp = self._call_serving(model, prompt)
            latency = (time.perf_counter() - t0) * 1000
            outs.append({"text": resp["text"], "model": model, "tier": tier,
                         "latency_ms": latency, "cost": self._cost(model, resp)})
        return outs
```

### Query del ahorro

```sql
SELECT date_trunc('day', ts) AS dia,
       sum(cost) AS coste_real,
       sum(tokens_in) * :precio_caro_in / 1000 + sum(tokens_out) * :precio_caro_out / 1000 AS coste_si_todo_caro,
       round(100 * (1 - sum(cost) /
          (sum(tokens_in)*:precio_caro_in/1000 + sum(tokens_out)*:precio_caro_out/1000)), 1) AS ahorro_pct
FROM main.ai_router.routing_log GROUP BY 1 ORDER BY 1;
```

### Verificador asíncrono (Job)

```python
queue = spark.table("main.ai_router.verification_queue").where("verified = false")
for row in queue.collect():
    ref = call_serving(TIER_ALTO, row.prompt)
    score = judge(row.response_text, ref)
    if score < UMBRAL:
        mark_routing_failure(row, ref, score)   # alimenta complexity_labeled
```

## Decisiones clave

- **Features baratas, no LLM para clasificar**: si usas un LLM para decidir el tier, te gastas el ahorro en el clasificador.
- **Mapa en tabla Delta**: cambiar el routing sin redeployar el endpoint es la mitad del valor.
- **Verificación asíncrona**: nunca metas la llamada al modelo caro en el path del usuario.

## Errores comunes

- Optimizar solo coste: sin verificación de calidad, el "ahorro" es ficticio.
- Hardcodear precios: cambian; tabla o config.
- Re-entrenar el clasificador sin deduplicar los fallos (sesgo hacia los casos difíciles repetidos).

## Cómo validarlo

1. 100 prompts T1 deben ir ~100% al modelo barato; si no, el clasificador está mal calibrado.
2. Fuerza un fallo (prompt T3 etiquetado como T1) → debe aparecer en `routing_log` como escalado.
3. La query de ahorro con tus datos de load test debe dar un % positivo y defendible.
