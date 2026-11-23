# Solución de referencia — ai-15 · Orquestación multi-agente

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
                ┌────────────── Supervisor (plan + delegate) ──────────────┐
                │        ▲                                  │              │
        memoria (Vector Search)                     subtareas             │
                │        │                                  ▼              │
        ┌───────┴──┐  ┌──┴───────┐  ┌──────────┐  ┌───────────────┐       │
        │ Research │  │ Análisis │  │ Redacción│  │ Código (tools)│       │
        └───────┬──┘  └──┬───────┘  └────┬─────┘  └──────┬────────┘       │
                └────────┴──────► Revisor ◄──────────────┘                │
                                  │                                       │
              confianza baja / acción sensible                            │
                                  ▼                                       │
                      escalation queue → HUMANO → aprueba/modifica/rechaza
```

Todo trazado con MLflow: un trace por tarea, un span por decisión/tool call.

## Código clave

### UC Function como herramienta

```sql
CREATE OR REPLACE FUNCTION main.ai_agents.top_pickup_zones(limit_n INT)
RETURNS TABLE(zone STRING, viajes BIGINT, importe_medio DOUBLE)
RETURN SELECT pickup_zone, count(*), avg(total_amount)
       FROM samples.nyctaxi.trips GROUP BY 1 ORDER BY 2 DESC LIMIT limit_n;
```

### Descomposición del supervisor

```python
PLAN_PROMPT = """Descompón la tarea en subtareas ordenadas.
Memoria relevante: {memorias}\nTarea: {task}
Devuelve JSON: {{subtasks: [{{desc, specialist, inputs, expected_output, depends_on, complexity}}]}}"""

plan = Plan.model_validate_json(ai_query(SUPERVISOR_MODEL, PLAN_PROMPT.format(...)))
```

### Memoria semántica

```python
def save_memory(task, outcome):
    text = f"Tarea: {task.goal}\nEnfoque: {outcome.approach}\nTools: {outcome.tools}\nResultado: {outcome.score}/5"
    vs_index.upsert([{"memory_id": uuid4().hex, "text": text,
                      "user": task.user, "ts": now(), "importance": outcome.score}])

def recall(goal, k=3):
    return vs_index.similarity_search(query_text=goal, num_results=k,
                                      filters={"user": current_user})
```

### Escalado

```python
def maybe_escalate(ctx):
    if ctx.action.sensitive or ctx.confidence < THRESH or ctx.specialist_failures >= 2:
        task_id = pause(ctx)
        queue.push({"task": task_id, "context": ctx.pack(),
                    "proposed_action": ctx.action, "level": approval_level(ctx)})
        notify_reviewer(task_id)
        return WAIT_HUMAN
    return CONTINUE
```

### Agente como modelo MLflow (deploy)

```python
import mlflow
with mlflow.start_run():
    mlflow.langchain.log_model(
        lc_model=agent_chain,
        artifact_path="agent",
        registered_model_name="main.ai_agents.orchestrator",
        resources=[
            DatabricksServingEndpoint(endpoint_name=SUPERVISOR_MODEL),
            DatabricksFunction(function_name="main.ai_agents.top_pickup_zones"),
            DatabricksVectorSearchIndex(index_name="main.ai_agents.memory_vs")])
```

## Decisiones clave

- **UC Functions como tools**: los permisos del agente son permisos UC reales — auditable, revocable, gobernado. Nada de tools con credenciales propias.
- **Revisor independiente del especialista**: quien produce no se evalúa a sí mismo (misma lección que `ai-05`).
- **Memoria con fecha de caducidad**: una memoria de hace 6 meses sobre "el modelo que mejor funciona" puede ser activamente dañina.

## Errores comunes

- Supervisor que también ejecuta: mezcla planificación con ejecución y el trace se vuelve ilegible.
- Escalado todo-o-nada: niveles granulares o el humano acaba ignorando la cola por ruido.
- Memoria sin filtro por usuario: fuga de contexto entre usuarios.

## Cómo validarlo

1. Tarea demo completa end-to-end con aprobación humana en el paso sensible.
2. Re-ejecuta la misma tarea una semana después: el plan usa la memoria (visible en el trace) y es mejor.
3. Mata un especialista a propósito: el sistema reintenta/reasigna y el trace muestra la recuperación.
