# cap-02 · Knowledge Assistant empresarial (RAG + evaluación continua)

> **Proyecto integrador IA** — une `ai-06` (RAG híbrido), `ai-01` (eval CI/CD) y `ai-13` (dataset auto-creciente) en un único producto.
> Basado en el informe *Databricks State of AI Agents*: **Knowledge Assistant** es uno de los 4 Agent Bricks y los casos de uso de experiencia de cliente concentran el **40%** del uso de IA empresarial. Las empresas con evaluación activa llevan a producción **6x** más proyectos.

**Nivel:** 🔴 · **Duración estimada:** 3–4 semanas
**Integra:** `ai-06`, `ai-01`, `ai-13` · **Prerrequisito:** `cap-01` recomendado

## El escenario

Tu empresa (la cadena de restaurantes de `cap-01`, o un dominio nuevo) tiene documentación dispersa: manuales operativos, políticas, recetas, guías de proveedores. Vas a construir un **asistente de conocimiento** que responde con citas verificadas, se evalúa continuamente en CI y cuyo dataset de evaluación crece solo desde el tráfico real.

## Qué hace al producto "de producción" y no un demo

1. **RAG híbrido** (vector + keyword) con reranking y citas verificadas — `ai-06`.
2. **CI/CD de comportamiento**: todo cambio de prompt/chunking/modelo pasa por la suite de eval con golden set y bloquea regresiones — `ai-01`.
3. **Dataset auto-creciente**: las preguntas reales (logs del endpoint) alimentan automáticamente el eval set — `ai-13`.

## Fases

### Semana 1 — Núcleo RAG

- [ ] Corpus: documentación sintética del dominio (30–50 documentos en Volume: manuales, políticas, FAQs).
- [ ] Ingesta + chunking (estrategia estructural por secciones como baseline) + índice híbrido Vector Search.
- [ ] Chain de generación grounded con citas [1][2] y verificación de citas.
- [ ] Endpoint serving `ask` con score de confianza compuesto.

### Semana 2 — Evaluación seria

- [ ] Golden set: 60+ pares pregunta-respuesta a mano (lookup, multi-hop, sin-respuesta, ambiguas) en Delta.
- [ ] Suite de eval con MLflow (correctness, faithfulness, retrieval relevance, citation accuracy).
- [ ] Job de eval en CI: GitHub Action que corre la suite en PRs que toquen prompts/config y bloquea regresiones críticas.
- [ ] Comparativa híbrido vs solo-denso con la suite (números en el README).

### Semana 3 — Flywheel de producción

- [ ] Inference tables activadas en el endpoint: todo el tráfico queda en Delta.
- [ ] Job nocturno: muestrea logs → detecta preguntas sin buena respuesta (baja confianza, 👎) → auto-etiqueta → añade al golden set (con cola de revisión humana).
- [ ] Dashboard: preguntas/día, confianza media, tasa de "no lo sé", temas sin cobertura en el corpus.

### Semana 4 — Pulido

- [ ] Demo end-to-end: pregunta difícil → respuesta citada → pregunta fuera de corpus → "no lo sé" → esa pregunta aparece al día siguiente en el golden set.
- [ ] Case study: "asistente con X% faithfulness, Y% citation accuracy, eval dataset auto-creciente de Z casos".

## Criterios de éxito

- [ ] Cambiar el prompt a peor rompe el CI (como en `ai-01`).
- [ ] Una pregunta sin respuesta en el corpus jamás se inventa contenido.
- [ ] El golden set crece semanalmente con tráfico real.

## Datos y URLs

- Corpus sintético propio; golden set manual. Docs: https://docs.databricks.com/
- Informe de contexto: *State of AI Agents* (Databricks) — Knowledge Assistant como patrón empresarial probado.

## ⚠️ Seguridad

- Las inference tables contienen preguntas de usuarios: gobierna su acceso con UC y anonimiza antes de reutilizar en el golden set.
