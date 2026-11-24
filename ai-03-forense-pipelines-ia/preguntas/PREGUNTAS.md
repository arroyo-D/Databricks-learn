# Preguntas de práctica — ai-03 / Forense de pipelines de IA

**P1.** MLflow Tracing registra de cada ejecución:
- A) Solo el resultado final
- B) El árbol completo: prompt exacto, llamadas a herramientas, respuestas intermedias, latencias y costes por paso
- C) Solo errores
- D) El código fuente

**P2.** El análisis forense de un fallo empieza por:
- A) Cambiar el prompt
- B) Reproducir el caso desde el trace guardado y localizar el paso exacto donde la cadena se desvía
- C) Reiniciar el endpoint
- D) Preguntar al LLM qué pasó

**P3.** Un "judge loop" peligroso es:
- A) Un LLM evaluando a otro
- B) Un bucle donde el juez y el generador comparten el mismo sesgo y se refuerzan: el error se vuelve invisible al sistema
- C) Un while infinito
- D) Dos prompts iguales

**P4.** Clasificar los fallos encontrados en taxonomía (retrieval malo, prompt ambiguo, herramienta rota, modelo alucina) sirve para:
- A) Rellenar informes
- B) Atacar la CAUSA y no el síntoma: cada categoría tiene una reparación distinta
- C) Medir al equipo
- D) Justificar más GPUs

**P5.** Los traces en Delta permiten:
- A) Nada nuevo
- B) Análisis agregado de fallos con SQL: ¿en qué paso se concentra el 70% de los errores? ¿desde qué versión?
- C) Borrar logs
- D) Acelerar el modelo

**P6.** La diferencia entre debugging y forense en IA:
- A) Ninguna
- B) Debug = arreglar el caso; forense = entender el patrón sistémico detrás de muchos casos y prevenirlo
- C) Forense es solo para producción
- D) Debug es automático

---

## Soluciones
1. **B** — Trace completo por paso: la caja negra del agente.
2. **B** — Reproducir y localizar el paso exacto del desvío.
3. **B** — Sesgo compartido juez-generador = ceguera sistémica.
4. **B** — Taxonomía de fallos → reparación dirigida.
5. **B** — Traces consultables = patrones de fallo con SQL.
6. **B** — Caso individual vs patrón sistémico.
