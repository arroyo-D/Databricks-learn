# Preguntas de práctica — ai-13 / Dataset de evaluación auto-creciente

**P1.** El "flywheel" de evaluación es:
- A) Un tipo de GPU
- B) El bucle donde el tráfico real de producción (inference tables) alimenta continuamente el golden set de evaluación
- C) Un dashboard
- D) Un cluster siempre encendido

**P2.** Las mejores candidatas para el eval set desde producción son:
- A) Las preguntas fáciles
- B) Las de baja confianza, feedback negativo o novedosas (clustering de temas nuevos): donde el sistema falla o no sabe
- C) Las más frecuentes
- D) Las más largas

**P3.** El auto-etiquetado con LLM de nuevos ejemplos debe pasar por:
- A) Producción directa
- B) Una cola de revisión humana: el LLM etiqueta rápido pero el humano valida antes de que entre al golden set
- C) Otro LLM idéntico
- D) Nadie

**P4.** El clustering de preguntas entrantes detecta:
- A) Duplicados de tablas
- B) Temas emergentes sin cobertura: nuevas necesidades de usuarios que el golden set actual no mide
- C) Errores de SQL
- D) Fraude

**P5.** El riesgo de un eval set que solo crece con tráfico real:
- A) Ninguno
- B) Deriva hacia lo que los usuarios YA preguntan bien: hay que mantener ejemplos duros históricos y adversarios (regresiones viejas no vuelven)
- C) Que sea grande
- D) Que tenga nulos

**P6.** Este módulo es el complemento de ai-01 porque:
- A) Usan el mismo prompt
- B) ai-01 ejecuta la evaluación; ai-13 asegura que el golden set que ejecuta sigue siendo representativo de la realidad
- C) Son el mismo proyecto
- D) ai-13 es más barato

---

## Soluciones
1. **B** — Producción → eval set → mejora → producción: el volante.
2. **B** — Donde el sistema falla está el valor de aprendizaje.
3. **B** — Auto-etiquetado + revisión humana: velocidad con verdad.
4. **B** — Temas emergentes = huecos del eval set y del producto.
5. **B** — Conservar los casos duros: anti-regresión permanente.
6. **B** — Ejecutor y abastecedor del golden set: pareja natural.
