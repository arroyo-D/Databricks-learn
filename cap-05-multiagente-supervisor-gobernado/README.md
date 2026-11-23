# cap-05 · Sistema multiagente supervisor gobernado (proyecto final)

> **Proyecto integrador final** — une `ai-15` (orquestación), `ai-05` (arbitraje) y `ai-11` (gateway gobernado) en el sistema más completo del programa.
> Basado en *State of AI Agents*: **Supervisor Agent es el caso de uso nº1** de Agent Bricks (37% del uso a octubre 2025, lanzado en julio 2025) y los sistemas multiagente crecieron **327% en cuatro meses**. Las empresas con gobernanza activa llevan a producción **12x** más proyectos — este proyecto lleva la gobernanza incorporada desde el diseño.

**Nivel:** 🔴🔴 Cumbre · **Duración estimada:** 4 semanas
**Integra:** `ai-15`, `ai-05`, `ai-11`, y consume datos de `cap-01`/`cap-02` si los hiciste

## El escenario

Un "analista virtual" para la dirección de la empresa: recibe peticiones complejas en lenguaje natural ("analiza por qué cayó la venta del restaurante X la semana pasada y prepara un resumen para el comité"), las descompone, ejecuta con especialistas (datos, documentos, redacción), **audita sus propias respuestas** con críticos independientes, y escala al humano antes de cualquier acción sensible. Todo el tráfico LLM pasa por el gateway gobernado.

## Arquitectura objetivo

```
petición compleja ──► AI Gateway (rate limits, budgets, inference tables)
        │
        ▼
Supervisor (plan + delegate, con memoria semántica)
   ├── Especialista datos ──► UC Functions (SQL gobernado)
   ├── Especialista docs ──► RAG de cap-02
   ├── Especialista análisis ──► código sandboxed
   └── Redactor
        │
        ▼
Panel de críticos (ai-05): exactitud / lógica / completitud → adjudicador
        │
        ├─ veredicto OK ──► entrega
        └─ dudas / acción sensible ──► cola humana ──► aprueba/modifica/rechaza
        │
MLflow Tracing: cada decisión, tool call, crítica y escalado
```

## Fases

### Semana 1 — Esqueleto multiagente

- [ ] Supervisor + 3 especialistas + revisor en LangGraph sobre Databricks, con UC Functions como tools (queries sobre los datos de `cap-01` o `samples.nyctaxi`).
- [ ] Plan estructurado (subtareas, dependencias, especialista, formato esperado).
- [ ] Todo el tráfico LLM enrutado vía serving endpoints con AI Gateway activado (usage tracking + inference tables).

### Semana 2 — Memoria y calidad

- [ ] Memoria de trabajo por tarea (Delta) + memoria semántica (Vector Search) con retrieval en planificación.
- [ ] Panel de 3 críticos independientes sobre cada entregable (distintos modelos por crítico) + adjudicador que resuelve desacuerdos con evidencia.
- [ ] El revisor puede devolver el trabajo al especialista con feedback concreto (máx 2 iteraciones → escalado).

### Semana 3 — Humano-en-el-bucle y gobernanza

- [ ] Triggers de escalado (confianza baja, acción sensible, fallo repetido, críticos en desacuerdo fuerte).
- [ ] Cola de aprobación con niveles (notificar / aprobar acción / aprobar plan / tomar control).
- [ ] Presupuestos y rate limits por "equipo" en AI Gateway; circuit breaker por proveedor.

### Semana 4 — Observabilidad y demo final

- [ ] Trace explorer: árbol completo de la tarea (decisiones, tools, críticas, escalados) con costes por agente.
- [ ] Replay de tareas pasadas con modificación de inputs.
- [ ] Escenario demo completo: análisis de caída de ventas → datos consultados con tools → documentación citada → críticos pillan una conclusión débil → re-trabajo → humano aprueba el resumen final → memoria guarda la lección.
- [ ] Narrativa de portfolio con números: coste por tarea, tasa de escalado, % de críticas confirmadas, mejora por memoria en tareas repetidas.

## Criterios de éxito

- [ ] Tarea compleja resuelta con ≥3 especialistas coordinados y trazable de punta a punta.
- [ ] Ninguna acción sensible se ejecuta sin aprobación humana.
- [ ] Los críticos detectan al menos un error plantado que el redactor dejó pasar.
- [ ] Todo el gasto LLM es visible por equipo en el dashboard del gateway.

## Datos y URLs

- Datos de `cap-01` o `samples.nyctaxi`. Docs: https://docs.databricks.com/ (Agent Framework, AI Gateway).
- Contexto: *State of AI Agents* — Supervisor Agent nº1, multi-modelo (78%), gobernanza 12x.

## ⚠️ Seguridad

- UC Functions con permisos mínimos (SELECT). La aprobación humana no es opcional en acciones de escritura.
- Memoria por usuario con borrado disponible; gobernada con UC.
