# Solución de referencia — ai-01 · Detección de regresiones en prompts y modelos

> ⚠️ Intenta el ejercicio por tu cuenta antes de leer esto. Es una solución de referencia, no la única válida.

## Arquitectura de referencia

```
prompts/email_classifier_vN.yaml (Git + Volume UC)
        │  PR que toca prompts/
        ▼
GitHub Action ──► Databricks CLI ──► Lakeflow Job "eval-email-classifier"
                                        │
                                        ├─ task 1: run_eval (notebook)
                                        │     ai_query(modelo, prompt, emails_golden)
                                        │     → eval_runs + eval_results (Delta)
                                        ├─ task 2: compare_vs_baseline
                                        │     deltas, regresiones, umbrales 3%/8%
                                        └─ task 3: veredicto → exit code
                                                  │
              AI/BI Dashboard ◄── eval_runs ──────┤
              SQL Alert → Slack webhook ◄─────────┘
```

## Paso a paso con código

### 1. Golden dataset (Delta)

```sql
CREATE SCHEMA IF NOT EXISTS main.ai_eval;

CREATE TABLE main.ai_eval.emails_golden (
  case_id STRING, email_text STRING, expected_category STRING,
  ideal_summary STRING, difficulty STRING, notes STRING
) TBLPROPERTIES (delta.enableChangeDataFeed = true);
```

Inserta los casos desde un notebook con un `spark.createDataFrame([...])`. Versiona el dataset: cada re-curado es un `INSERT`/`MERGE` nuevo, y `DESCRIBE HISTORY main.ai_eval.emails_golden` te da el historial.

### 2. Ejecutar la feature con `ai_query`

```sql
SELECT case_id,
  ai_query('databricks-meta-llama-3-3-70b-instruct',
           concat(:system_prompt, '\nEmail: ', email_text),
           responseFormat => '{"type":"json_object"}'
  ) AS raw_output
FROM main.ai_eval.emails_golden
```

Parsea el JSON con `from_json(raw_output, 'category STRING, summary STRING')`. En Python, captura latencia con `time.perf_counter()` por llamada si lo haces desde la API de serving en vez de SQL.

### 3. LLM-as-judge para el resumen

```sql
ai_query('databricks-meta-llama-3-3-70b-instruct',
  concat('Puntúa 1-5 la relevancia de este resumen respecto al email. Responde solo el número.\nEmail: ',
         email_text, '\nResumen: ', parsed.summary)) AS summary_score
```

### 4. Comparación y umbrales

```python
THRESH_WARN, THRESH_CRIT = 0.03, 0.08
prev = spark.sql("SELECT * FROM main.ai_eval.eval_runs ORDER BY run_ts DESC LIMIT 1 OFFSET 1")
delta = current_pass_rate - prev_pass_rate
status = "crit" if delta <= -THRESH_CRIT else "warn" if delta <= -THRESH_WARN else "ok"
dbutils.jobs.taskValues.set("eval_status", status)
```

Casos flipped: `FULL OUTER JOIN` del run actual con el anterior por `case_id`, filtrando donde `passed` cambió.

### 5. Job con Asset Bundles (`databricks.yml`)

```yaml
resources:
  jobs:
    eval_email_classifier:
      tasks:
        - task_key: run_eval
          notebook_task: {notebook_path: ./notebooks/run_eval}
        - task_key: compare
          depends_on: [{task_key: run_eval}]
          notebook_task: {notebook_path: ./notebooks/compare}
```

### 6. GitHub Action (extracto)

```yaml
on:
  pull_request:
    paths: ['prompts/**']
jobs:
  eval:
    runs-on: ubuntu-latest
    steps:
      - uses: databricks/setup-cli@main
      - run: databricks bundle deploy -t dev
      - run: databricks bundle run eval_email_classifier -t dev
        env:
          DATABRICKS_TOKEN: ${{ secrets.DATABRICKS_TOKEN }}
```

## Decisiones clave

- **Delta como store de evals**: time travel te da "¿qué puntuaba este caso hace 2 semanas?" gratis.
- **Judge con el mismo modelo** que evalúas es aceptable en V1; documéntalo y considera un modelo distinto como judge en V2.
- **Drift lento separado**: un run ruidoso no debería alertar; una caída sostenida de 7 runs sí.

## Errores comunes

- Generar el golden dataset con LLM → el eval mide acuerdo consigo mismo, no calidad.
- No fijar `temperature` baja (0–0.2): el clasificador debe ser determinista o el ruido se come la señal.
- Guardar el token en el YAML del workflow → rota el token y muévelo a GitHub Secrets.

## Cómo validarlo

1. Degrada el prompt a propósito (quita few-shots) → la eval debe caer >8% y el job debe fallar.
2. Restaura el prompt → pass rate vuelve al baseline.
3. `SELECT * FROM eval_runs ORDER BY run_ts` muestra la historia completa.
