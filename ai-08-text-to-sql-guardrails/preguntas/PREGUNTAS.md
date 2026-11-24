# Preguntas de práctica — ai-08 / Text-to-SQL con guardrails

**P1.** Un guardrail en text-to-SQL es:
- A) Un firewall de red
- B) Una validación que bloquea o corrige el SQL generado antes de ejecutarlo (solo SELECT, tablas permitidas, límite de filas, timeout)
- C) Un índice
- D) Un comentario en el código

**P2.** El primer guardrail de un sistema text-to-SQL es:
- A) Revisar el plan de ejecución
- B) Ejecutar SIEMPRE con un usuario de solo lectura y permisos UC mínimos sobre las tablas expuestas
- C) Limitar a 100 filas
- D) Revisar el prompt

**P3.** ¿Por qué Genie responde mejor con PK/FK y COMMENTs?
- A) Porque son obligatorios
- B) El modelo necesita el contexto semántico: qué significa cada columna y cómo se unen las tablas
- C) Porque acelera el SQL
- D) Porque cachea respuestas

**P4.** Un SQL generado con JOIN fan-out (duplica filas) es el ejemplo de que:
- A) El SQL es correcto si corre
- B) "Ejecuta sin error" ≠ "correcto": los resultados deben validarse (conteos esperados, comparación con consulta de referencia)
- C) Hay que usar más LLM
- D) El esquema está mal siempre

**P5.** La evaluación del text-to-SQL usa:
- A) Exactitud de texto del SQL
- B) Execution accuracy: ¿el resultado del SQL generado coincide con el de la consulta de referencia del golden set?
- C) BLEU score
- D) Latencia

**P6.** Preguntas que el sistema debe RECHAZAR:
- A) Las difíciles
- B) Las que piden datos fuera del esquema permitido o son ambiguas sin interpretación razonable: un buen sistema sabe decir "no puedo"
- C) Las de directivos
- D) Las agregaciones

---

## Soluciones
1. **B** — Validación pre-ejecución: la capa de seguridad del SQL generado.
2. **B** — Mínimo privilegio como base: el peor SQL posible no puede romper ni filtrar.
3. **B** — Semántica explícita = mejor generación.
4. **B** — Ejecutar ≠ acertar: validación de resultados.
5. **B** — Lo que importa es el resultado, no la forma del SQL.
6. **B** — Saber rechazar es una feature, no un bug.
