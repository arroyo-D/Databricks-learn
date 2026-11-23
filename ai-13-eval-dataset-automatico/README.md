# ai-13 · Generador automático de datasets de evaluación desde logs de producción

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 13: Automated Eval Dataset Generator from Production Logs* de la guía BASWE.

**Nivel:** 🟡 Intermedio · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `ai-01`, `ai-11` (inference tables)

## Qué vas a construir

Un sistema que mina continuamente los logs de las llamadas LLM en producción (las inference tables de Databricks), identifica interacciones interesantes, edge cases y fallos, y los convierte automáticamente en **casos de evaluación etiquetados** — un eval dataset que crece solo y representa el tráfico real, no las suposiciones de hace seis meses.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| PostgreSQL/ClickHouse de logs | **Inference tables** de Model Serving (Delta, automáticas) |
| HDBSCAN clustering | Spark ML / scikit-learn en cluster (embeddings + clustering) |
| GPT-4o como labeler | `ai_query` con modelo fuente |
| Cron nocturno | **Lakeflow Job** programado |
| Dashboard de curación | AI/BI Dashboard + queries de revisión |

## Fases del proyecto

### Fase 1 — Ingesta y normalización de logs (días 1–3)

- [ ] Activa inference tables en tus endpoints de proyectos anteriores (ai-01/02/07): cada request/response ya aterriza en Delta.
- [ ] Esquema normalizado unificado: prompt, system prompt, modelo, respuesta, latencia, tokens, señales de feedback, feature origen, timestamp.
- [ ] Redacción de PII configurable (regex + detección de entidades) antes de persistir en la tabla de análisis.
- [ ] Muestreo estratificado: uniforme, por feature/modelo/estado-de-error, y potenciado por señal (👎, retries, latencia alta). Configurable por ejecución.

### Fase 2 — Clasificador de interacciones (días 3–6)

- [ ] Embebe los prompts muestreados y clusterea (KMeans/HDBSCAN): descubre las categorías naturales de tu tráfico; nómbralas por ejemplos representativos.
- [ ] Detecta outliers: prompts que no clusterean bien, respuestas con baja confianza, usuarios que reintentaron reformulando, respuestas anormalmente largas/cortas, contenido filtrado. Son tus candidatos más valiosos.
- [ ] Puntuación de calidad con judge (1–5): buenas → casos positivos; malas → casos negativos.
- [ ] Estimador de dificultad: simple / moderada / difícil / adversarial.

### Fase 3 — Auto-etiquetado (días 6–9)

- [ ] Respuestas de referencia: modelo fuerte + chain-of-thought genera la golden answer; para preguntas subjetivas, genera una rúbrica en vez de respuesta única.
- [ ] Etiquetas multidimensionales: score esperado, comportamiento esperado (responder/rechazar/pedir clarificación), aserciones obligatorias, aserciones prohibidas (trampas de alucinación), dificultad, categoría.
- [ ] Enrutado por confianza: acuerdo alto entre múltiples pasadas del labeler → auto-add; bajo → cola de revisión humana.
- [ ] Dedupe contra el dataset existente (coseno > 0.92 → skip) + métricas de cobertura por cluster (prioriza categorías infrarrepresentadas).

### Fase 4 — Eval runner y regresiones (días 9–11)

- [ ] Harness configurable: cualquier endpoint → ejecuta el dataset completo → puntúa con rúbricas → informe por categoría, dificultad y dimensión (conecta con `ai-01`).
- [ ] Detección de regresiones entre runs: nuevos fallos, nuevos aciertos, cambios de score, desplazamientos por categoría.
- [ ] Tracking del dataset: total de casos en el tiempo, distribución, % auto-etiquetado vs revisado, heatmap de cobertura, "frescura" (% de casos de los últimos 30 días).

### Fase 5 — Curación y automatización (días 11–13)

- [ ] Explorador del dataset: navega por categoría/dificultad/calidad, ve la interacción completa, aprueba/edita/rechaza etiquetas automáticas.
- [ ] Cola de revisión: candidatos de baja confianza con contexto (casos similares existentes) y acciones rápidas.
- [ ] Job nocturno: muestrea logs nuevos → clasifica → auto-etiqueta → deduplica → añade al dataset. El eval suite crece mientras duermes.

### Fase 6 — Pulido (días 13–14)

- [ ] Demo ≤4 min: logs procesándose, clusters revelando patrones, auto-etiquetado, eval runner pillando una regresión, gráfico de crecimiento.
- [ ] Narrativa: "convierte logs de producción en un eval dataset que crece solo: X casos en Y categorías con Z% de precisión de auto-etiquetado".

## Criterios de éxito

- [ ] El dataset crece semanalmente sin curación manual masiva.
- [ ] Los casos generados pillan una regresión real (provocada) en el eval runner.
- [ ] Cobertura balanceada por clusters, no solo los casos fáciles.

## Datos y URLs

- Logs: los de tus propios endpoints de los módulos anteriores (tráfico sintético).
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → inference tables, Agent Evaluation).

## ⚠️ Seguridad

- PII: redacción ANTES de persistir, no después. Revisa las reglas con casos reales.
- No apuntes este pipeline a endpoints con datos de usuarios reales en este proyecto.
