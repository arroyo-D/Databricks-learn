# Preguntas de práctica — ai-11 / AI Gateway gobernado

**P1.** AI Gateway se sitúa:
- A) Detrás del modelo
- B) Delante de los endpoints LLM como punto único de control: rate limits, budgets, guardrails y logging de TODO el tráfico
- C) En el cliente
- D) En Unity Catalog

**P2.** Las inference tables registran:
- A) Solo errores
- B) Cada petición y respuesta que cruza el endpoint en tablas Delta: auditoría, evaluación y análisis posterior con SQL
- C) Solo métricas agregadas
- D) Los embeddings

**P3.** Un rate limit por usuario/equipo en el gateway evita:
- A) La latencia
- B) Que un workload descontrolado consuma la cuota/presupuesto de todos (noisy neighbour de tokens)
- C) Los prompt injections
- D) El SRM

**P4.** External Models en el gateway permite:
- A) Entrenar modelos externos
- B) Gobernar con las mismas reglas (límites, logging, guardrails) llamadas a proveedores externos (OpenAI, Anthropic...)
- C) Descargar modelos
- D) Evitar API keys

**P5.** Un budget (presupuesto) en AI Gateway:
- A) Es un límite de velocidad
- B) Un tope de gasto por período/equipo que bloquea o alerta al superarse: gobernanza de costes, no solo de tráfico
- C) Una tabla Delta
- D) Un tipo de endpoint

**P6.** En Free Edition la versión de este proyecto es:
- A) Imposible
- B) Gateway sobre los endpoints pay-per-token disponibles + análisis de inference tables: el patrón de gobierno se aprende igual
- C) Solo lectura de docs
- D) Con Ollama sin más

---

## Soluciones
1. **B** — Punto único de control del tráfico LLM.
2. **B** — Todo el tráfico auditado en Delta: la base de ai-13 y ai-03.
3. **B** — Límites por consumidor = presupuesto compartido protegido.
4. **B** — Misma gobernanza para modelos internos y externos.
5. **B** — Budget = gobernanza económica del LLM.
6. **B** — El patrón se aprende con lo disponible en Free.
