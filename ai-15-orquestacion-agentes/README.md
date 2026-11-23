# ai-15 · Orquestación multi-agente con herramientas, memoria y humano-en-el-bucle

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 15: Agent Orchestration System* de la guía BASWE. Proyecto final del track.

**Nivel:** 🔴 Avanzado · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `ai-03`, `ai-05`, `ai-06`

## Qué vas a construir

Una plataforma multi-agente: un **supervisor** descompone tareas complejas, delega en **agentes especialistas** con herramientas (búsqueda, consulta SQL, ejecución de código), mantiene **memoria** a corto y largo plazo, y escala a un **humano** cuando la confianza es baja o la acción es sensible — con trazabilidad completa de cada decisión.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| LangGraph + múltiples proveedores | **Mosaic AI Agent Framework** + LangGraph en Databricks (integración nativa) |
| Herramientas custom + MCP | **UC Functions** como tools (SQL, Python) + tools externas |
| Redis (memoria corta) + ChromaDB (larga) | Delta (working memory por task) + **Vector Search** (memoria semántica) |
| PostgreSQL estado | Tablas Delta `agent_tasks`, `agent_memory`, `escalations` |
| OpenTelemetry | **MLflow Tracing** con spans por agente/decisión |
| Review UI | Databricks App + endpoints de aprobación |

## Fases del proyecto

### Fase 1 — Arquitectura de agentes (días 1–4)

- [ ] 3 capas: Supervisor (planifica y delega), Especialistas (research, análisis de datos, redacción, código) con tools de dominio, Revisor (valida outputs antes de volver al supervisor).
- [ ] Motor de descomposición: tarea compleja → lista ordenada de subtareas con dependencias, especialista asignado, inputs requeridos, formato esperado, complejidad estimada (structured output).
- [ ] Registro de herramientas: **UC Functions** (`query_taxi_data`, `calculate_stats`) + tools Python registradas con nombre, descripción, esquemas de entrada/salida, qué especialistas pueden usarlas. Cada invocación logueada.
- [ ] State machine en LangGraph sobre Databricks con esquemas de estado tipados.

### Fase 2 — Sistema de memoria (días 4–7)

- [ ] Memoria de trabajo (por tarea): plan actual, outputs de subtareas completadas, resultados intermedios, errores. En Delta, scoped al task_id, se limpia al terminar.
- [ ] Memoria semántica a largo plazo: al completar cada tarea, extrae y embebe (qué pidió el usuario, qué enfoque funcionó, qué tools se usaron, preferencias observadas) → Vector Search.
- [ ] Retrieval en planificación: antes de planificar, el supervisor consulta memoria: tareas similares pasadas, enfoques que funcionaron/fallaron, contexto del usuario.
- [ ] Gestión de memoria: scoring de importancia, consolidación (fusionar memorias similares), expiración, dashboard de "qué recuerda el sistema", endpoint de borrado (derecho al olvido).

### Fase 3 — Humano-en-el-bucle (días 7–10)

- [ ] Triggers de escalado: confianza del plan < umbral, especialista falla 2 veces, operación sensible (borrado, envío externo, transacción), score del revisor < umbral, petición explícita del usuario.
- [ ] Cola de aprobación: pausa la ejecución, empaqueta contexto completo (tarea, plan, pasos hechos, acción propuesta con razonamiento), notifica y espera: aprobar / modificar / rechazar / tomar el control.
- [ ] Niveles granulares: Notificar (sigue pero informa), Aprobar acción, Aprobar plan completo, Tomar el control.
- [ ] Interfaz de revisión: contexto + progreso, punto de decisión, acción propuesta con razonamiento, memorias relevantes, botones de decisión, chat para preguntar al agente antes de decidir.

### Fase 4 — Observabilidad y depuración (días 10–12)

- [ ] Trace tree por tarea (MLflow): decisiones del supervisor, llamadas a tools de cada especialista, evaluaciones del revisor, memorias recuperadas y su influencia, escalados y resoluciones.
- [ ] Explorador de traces: árbol visual con nodo por agente (qué decidió, qué tools llamó, latencia, coste, errores), coloreado por estado.
- [ ] Coste y rendimiento por tarea: tokens por agente/modelo, llamadas a tools, tiempo total, tiempo de revisión humana, coste total; agregados por tipo de tarea.
- [ ] Replay: re-ejecuta cualquier tarea pasada paso a paso, modifica un input en cualquier punto y compara la divergencia.

### Fase 5 — Integración y tests e2e (días 12–13)

- [ ] Escenario demo: tarea de investigación que requiere búsqueda + extracción + análisis + resumen escrito. Supervisor descompone, especialistas en paralelo, revisor devuelve un output malo, memoria informa una decisión, humano aprueba el entregable.
- [ ] Tests e2e: descomposición válida, especialistas usan tools correctamente, revisor caza outputs malos plantados, memoria mejora planes de tareas repetidas, escalado en los momentos correctos, recuperación elegante ante fallo de un agente.

### Fase 6 — Pulido (días 13–14)

- [ ] Demo ≤5 min del ciclo completo con el explorador de traces.
- [ ] Narrativa: "infraestructura de producción para flujos de IA autónomos", liderando con el diagrama de jerarquía de agentes.

## Criterios de éxito

- [ ] Una tarea compleja se descompone y ejecuta con ≥3 especialistas distintos.
- [ ] Una operación sensible se pausa hasta aprobación humana (nunca se ejecuta sola).
- [ ] La segunda ejecución de una tarea similar es mejor gracias a la memoria (demostrable).

## Datos y URLs

- Datos: los de módulos anteriores (`samples.nyctaxi`, tus tablas). Herramientas externas: APIs públicas verificadas de [../docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md).
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → Agent Framework, Agent Bricks).

## ⚠️ Seguridad

- Las UC Functions definen QUÉ pueden tocar los agentes: permisos mínimos (SELECT) y nada de funciones de escritura salvo necesidad justificada.
- La memoria persistente puede contener datos de usuario: con borrado implementado y acceso gobernado por UC.
