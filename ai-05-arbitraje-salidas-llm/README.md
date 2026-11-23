# ai-05 · Arbitraje de salidas LLM (multi-crítico + adjudicador)

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 5: LLM Output Arbitration System* de la guía BASWE.

**Nivel:** 🔴 Avanzado · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `ai-01`, `ai-03`

## Qué vas a construir

Un pipeline multi-agente que toma cualquier salida de un LLM, la envía a **tres críticos especializados** (exactitud factual, consistencia lógica, completitud) que la evalúan independientemente, detecta sus desacuerdos y los resuelve con un **adjudicador** que emite un veredicto final con score de confianza y evidencias.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| Críticos en GPT-4o / Claude / Llama local | 3 serving endpoints distintos: Foundation Model APIs + External Models vía AI Gateway |
| LangGraph state graph | LangGraph ejecutándose en notebooks/Jobs de Databricks |
| instructor + Pydantic | Structured output de `ai_query` (responseFormat JSON schema) + Pydantic |
| SQLite audit | Tabla Delta `arbitrations` |
| FastAPI | **Model Serving endpoint** (pyfunc que orquesta el grafo) |

## Fases del proyecto

### Fase 1 — Arquitectura de críticos (días 1–3)

- [ ] Define los 3 roles: FactualAccuracyCritic, LogicalConsistencyCritic, CompletenessCritic.
- [ ] Formato de crítica (Pydantic): `dimension`, `score` 1–5, `issues[]` (cita literal del texto, problema, severidad), `self_confidence`.
- [ ] Asigna un modelo distinto a cada crítico (p. ej. Llama-70B, Claude Sonnet vía Gateway, Llama-8B). Los desacuerdos entre modelos son la señal valiosa.

### Fase 2 — Orquestación con LangGraph (días 3–6)

- [ ] State graph: parseo de input → dispatch **en paralelo** a los 3 críticos → recolección → detección de desacuerdos → adjudicación → veredicto.
- [ ] Fan-out paralelo (no secuencial): latencia total ≈ latencia del crítico más lento.
- [ ] Detector de desacuerdos: severidades que difieren en >2 puntos, issues que un crítico ve y los demás no, contradicciones directas.
- [ ] Resiliencia: si un endpoint falla, retry con backoff; si persiste, veredicto degradado con nota de menor confianza en esa dimensión. Si los 3 coinciden en que es perfecto → cortocircuito sin adjudicador.

### Fase 3 — Adjudicador (días 6–8)

- [ ] Recibe: salida original, 3 críticas, lista de desacuerdos. Razona cada desacuerdo explícitamente.
- [ ] Resolución basada en evidencia: verifica claims factuales, traza cadenas de razonamiento, re-lee la pregunta original para completitud.
- [ ] Veredicto final: score 1–10, nivel de confianza, issues confirmados (con evidencia), flags descartados (con razonamiento), resumen de un párrafo.

### Fase 4 — Explorador de veredictos (días 8–10)

- [ ] Vista del texto con anotaciones inline: rojo (confirmado), amarillo (flag dudoso), verde (validado explícitamente).
- [ ] Panel comparativo de los 3 críticos: acuerdos en verde, desacuerdos en naranja (AI/BI dashboard o app sencilla).
- [ ] Modo batch: múltiples salidas, tabla ordenable por score/issues/confianza.

### Fase 5 — API y analítica (días 10–12)

- [ ] Serving endpoint `arbitrate`: entrada = salida LLM (+ prompt original opcional), salida = veredicto completo.
- [ ] Todo queda en `arbitrations` (Delta): críticas, desacuerdos, veredicto, latencias, costes.
- [ ] Meta-analítica con SQL: qué crítico encuentra más issues, cuál es más revocado por el adjudicador, tasa de acuerdo/desacuerdo, tipos de fallo más comunes.

### Fase 6 — Pulido (días 12–14)

- [ ] 4 casos demo: error factual plantado, argumento ilógico, respuesta que no responde a la pregunta, respuesta genuinamente buena (debe pasar limpia).
- [ ] Narrativa: "sistema donde modelos de IA auditan el trabajo de otros modelos", con la analítica que demuestra que la crítica multi-modelo pilla lo que la auto-evaluación no.

## Criterios de éxito

- [ ] Un error factual plantado es detectado al menos por 2 de 3 críticos y confirmado por el adjudicador.
- [ ] Un fallo de un endpoint no tumba el sistema (veredicto degradado documentado).
- [ ] La meta-analítica sobre ≥50 arbitrajes muestra patrones por crítico.

## Datos y URLs

- Casos de prueba: los escribes tú con errores plantados (más instructivo que datos reales).
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → AI Gateway, Model Serving).

## ⚠️ Seguridad

- External models (Claude, GPT) solo vía AI Gateway con las keys en su configuración; nunca en notebooks.
- No arbitres textos con datos personales en un repo/demo pública.
