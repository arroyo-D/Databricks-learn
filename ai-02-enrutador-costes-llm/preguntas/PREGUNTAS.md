# Preguntas de práctica — ai-02 / Enrutador de costes LLM

**P1.** La idea central del enrutador de costes es:
- A) Usar siempre el modelo más grande
- B) Enviar cada petición al modelo más barato capaz de resolverla bien (clasificador de dificultad/intención por delante)
- C) Cachear todo
- D) Reducir tokens de salida

**P2.** El riesgo principal de enrutar a modelos pequeños:
- A) Ninguno
- B) Caída de calidad en los casos difíciles mal clasificados: el enrutador se evalúa con golden set como cualquier componente
- C) Más latencia
- D) Incompatibilidad de formatos

**P3.** Las system tables de billing/usage permiten:
- A) Ver el catálogo
- B) Atribuir consumo (tokens/coste) por endpoint, usuario o workload con SQL para optimizar gasto
- C) Crear endpoints
- D) Entrenar modelos

**P4.** La métrica de éxito del enrutador NO es solo "ahorro" sino:
- A) Velocidad
- B) Ahorro manteniendo la calidad por encima del umbral: coste por petición con calidad garantizada
- C) Nº de modelos usados
- D) Tokens totales

**P5.** Una decisión de routing típica usa como features:
- A) El color del logo
- B) Longitud, presencia de código/datos estructurados, idioma, intención clasificada
- C) La hora del día
- D) El usuario que pregunta

**P6.** En Free Edition, la alternativa al despliegue de varios endpoints propios es:
- A) No hacer el proyecto
- B) `ai_query` contra los Foundation Models pay-per-token disponibles + fallback Ollama local para desarrollo
- C) Pagar un mes
- D) Usar solo reglas

---

## Soluciones
1. **B** — El modelo más barato que mantiene la calidad para ESA petición.
2. **B** — El clasificador de routing también se evalúa: error de routing = error de calidad.
3. **B** — Atribución de costes con SQL sobre system tables.
4. **B** — Coste CON calidad garantizada: la métrica compuesta.
5. **B** — Features de contenido e intención, no de contexto irrelevante.
6. **B** — Pay-per-token serverless + Ollama de desarrollo.
