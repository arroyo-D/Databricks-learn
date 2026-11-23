# Solución de referencia — cap-05 · Multiagente supervisor gobernado

> ⚠️ Detalles en `ai-15`, `ai-05` y `ai-11`. Aquí: cómo se integran los tres sistemas.

## Las tres capas y sus responsabilidades

| Capa | Módulo | Responsabilidad |
|---|---|---|
| Orquestación | ai-15 | descomponer, delegar, recordar, escalar |
| Calidad | ai-05 | auditar el resultado con críticos independientes |
| Gobernanza | ai-11 | presupuestos, límites, fallback, observabilidad |

La regla de oro: **ninguna capa hace el trabajo de otra**. El supervisor no juzga calidad (los críticos sí), los críticos no gestionan costes (el gateway sí), el gateway no decide planes (el supervisor sí).

## Puntos de unión

### Todo el tráfico por el gateway

```python
# los agentes nunca llaman a un endpoint directo:
SUPERVISOR_MODEL = endpoint_via_gateway("supervisor-llm")   # con budget del equipo
# así inference tables capturan TODA la actividad de TODOS los agentes
```

### Críticos como paso del grafo, no post-proceso

```python
g = StateGraph(AgentState)
...
g.add_node("critics", run_critic_panel)        # ai-05: fan-out paralelo
g.add_node("adjudicate", adjudicate)
g.add_conditional_edges("redactor",
    lambda s: "critics" if s["deliverable"] else "supervisor")
g.add_conditional_edges("adjudicate",
    lambda s: "deliver" if s["verdict"].score >= 7 else
              "rework" if s["rework_count"] < 2 else "escalate")
```

### Escalado unificado

```python
ESCALATION_REASONS = {
    "sensitive_action": "approve_action",
    "low_confidence_plan": "approve_plan",
    "critics_disagree_strongly": "approve_action",
    "specialist_failed_twice": "take_over",
}
```

### Coste por tarea (para tu demo)

```sql
SELECT task_id, count(DISTINCT agent) AS agentes,
       sum(tokens_in + tokens_out) AS tokens,
       sum(cost_usd) AS coste_total,
       sum(if(step='critic', cost_usd, 0)) * 1.0 / sum(cost_usd) AS pct_en_calidad
FROM cap_agents.task_costs GROUP BY 1;
-- "la auditoría de calidad cuesta un X% del total" es un gran dato para entrevistas
```

## Errores comunes

- Críticos con el mismo modelo que el redactor: punto ciego compartido (lección de ai-05).
- Gateway añadido "al final": sin inference tables desde el día 1, no tienes datos para presupuestos ni para el demo.
- Memoria sin caducidad: decisiones con contexto obsoleto son peores que sin contexto.

## Cómo validarlo

1. Demo completo del escenario con aprobación humana en el paso sensible.
2. Planta una conclusión débil a propósito: los críticos la detectan y el sistema re-trabaja.
3. Repite una tarea una semana después: el plan mejora por memoria (visible en el trace).
4. Dashboard del gateway: coste por equipo y por tarea, cuadra con los traces.
