# ai-12 · Feature flags para IA con rollout gradual y rollback automático

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 12: AI Feature Flag System* de la guía BASWE.

**Nivel:** 🔴 Avanzado · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `ai-09`, `dbx-05-structured-streaming`

## Qué vas a construir

Un sistema de feature flags diseñado para features de IA: rollouts graduales por porcentaje, **monitorización continua de calidad** durante el rollout y **rollback automático** cuando la calidad cae por debajo del umbral — porque una feature de IA no falla en binario, falla en gradiente.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| PostgreSQL + Redis para flags | Tablas Delta (config, estados) + serving endpoint que las lee con cache TTL |
| SDK Python custom | Módulo `flag_client` compartido + Widget de config |
| LLM-as-judge asíncrono | Lakeflow Job/Streaming que puntúa outputs con `ai_query` |
| Rolling quality windows | **Lakehouse Monitoring** + queries de ventana sobre Delta |
| Rollback con Slack | SQL Alert / webhook + actualización automática del flag |

## Fases del proyecto

### Fase 1 — Motor de flags (días 1–3)

- [ ] Esquema de flag de IA: rollout_pct (0–100), quality_threshold, condiciones de rollback, configuración baseline (prompt/modelo anterior o fallback no-IA), configuración experimental.
- [ ] SDK `flag_client.evaluate(flag_name, user_context)`: asignación consistente por hash, rollout porcentual, cache local de config, degradación elegante a baseline si el servicio falla.
- [ ] Reglas de targeting: por segmento (internos → beta → todos), por metadata del request, allowlist/blocklist de usuarios.
- [ ] API/tabla de gestión: CRUD de flags, `set_rollout`, `pause`, `rollback`; todo logueado con actor y motivo.

### Fase 2 — Monitorización de calidad (días 3–6)

- [ ] Métricas por flag: LLM-as-judge 1–5, feedback de usuario (👍/👎), latencia, error rate.
- [ ] Evaluación asíncrona: cada respuesta servida bajo flag se encola; un job de streaming la puntúa y escribe a `flag_quality_log` — sin añadir latencia al usuario.
- [ ] Ventanas rodantes: media, desviación, P10 de calidad y tendencia (mejorando/estable/degradando) en ventanas de 100 evaluaciones / 1h / 24h. Comparación experimental vs baseline continua.
- [ ] Trigger de rollback automático: P10 < umbral durante N evaluaciones consecutivas → rollout a 0%, alerta con datos, evento logueado, cooldown anti-flapping.

### Fase 3 — Rollout gradual automatizado (días 6–9)

- [ ] Plan por etapas: 1% (2h) → 5% (6h) → 25% (24h) → 50% (24h) → 100%. En cada frontera: calidad ≥ umbral → avanza; si no → pausa y alerta.
- [ ] Análisis canary: en cada etapa, test estadístico experimental vs baseline (reutiliza lo de `ai-09`); solo avanza si el experimental **no es significativamente peor**.
- [ ] Dashboard de rollout: etapa y % actual, comparativa de calidad en el tiempo, próximas transiciones y condiciones, pausas/rollbacks con motivos, ETA a rollout completo.
- [ ] Modo shadow: la variante experimental corre sobre todo el tráfico pero no se muestra a usuarios; se loguea qué se habría servido y se evalúa offline. Caza fallos catastróficos antes de exponer a nadie.

### Fase 4 — Dashboard e integración (días 9–11)

- [ ] Vista de flags: estado (off/rolling/on/rolled back), calidad en vivo, progreso del plan, botón de rollback con confirmación.
- [ ] Analítica histórica: calidad durante todo el rollout, eventos de rollback y causas, tiempo-hasta-rollout-completo por feature.
- [ ] Doc del SDK: integración en 3–5 líneas con ejemplos (fallback IA/no-IA, A/B de prompts, cambio de modelo).

### Fase 5 — Demo e integración (días 11–13)

- [ ] App demo: generador de asuntos de email con el flag. Muestra: 0% → rollout gradual → variante mala deliberada detectada y auto-rollback → variante buena llegando a 100%.
- [ ] Tests: asignación consistente, rollback dispara en degradación, etapas avanzan con calidad, SDK sobrevive a caída del servicio de flags.

### Fase 6 — Pulido (días 13–14)

- [ ] Demo ≤4 min del ciclo completo con la alerta de rollback incluida.
- [ ] Narrativa: "los feature flags tradicionales no sirven para IA porque las features de IA fallan en gradiente, no en binario".

## Criterios de éxito

- [ ] Una variante degradada es auto-retirada sin intervención humana.
- [ ] El plan de etapas avanza solo cuando la calidad lo permite (canary con estadística).
- [ ] Modo shadow detecta un desastre con 0 usuarios afectados.

## Datos y URLs

- Tráfico sintético generado por ti.
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → Lakehouse Monitoring).

## ⚠️ Seguridad

- El flag de configuración puede cambiar qué modelo sirve: acceso a la tabla de config restringido con UC.
- Log de shadow mode contiene outputs no servidos: trátalo con el mismo cuidado que los servidos.
