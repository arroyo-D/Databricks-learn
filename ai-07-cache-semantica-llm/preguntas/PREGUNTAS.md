# Preguntas de práctica — ai-07 / Caché semántica para LLMs

**P1.** Una caché semántica difiere de una caché exacta en que:
- A) Es más grande
- B) Sirve respuestas para preguntas DIFERENTES pero semánticamente equivalentes ("¿horario?" ≈ "¿a qué hora abrís?")
- C) Usa Redis
- D) No necesita embeddings

**P2.** El componente crítico de la caché semántica es:
- A) El TTL
- B) El umbral de similitud: demasiado bajo sirve respuestas a preguntas distintas (falsos hits peligrosos); demasiado alto no ahorra nada
- C) El modelo de lenguaje
- D) El índice de Delta

**P3.** Un falso hit de caché es peor que un miss porque:
- A) Cuesta más
- B) Devuelve con confianza una respuesta que correspondía a OTRA pregunta: error silencioso de cara al usuario
- C) Es más lento
- D) Llena el disco

**P4.** La caché se invalida/actualiza cuando:
- A) Nunca
- B) Cambia la información subyacente (precios, políticas): la caché hereda el problema de frescura de los datos
- C) Cada hora fija
- D) Al reiniciar

**P5.** Las métricas del sistema son:
- A) Solo hit rate
- B) Hit rate, ahorro de tokens/coste, latencia servida vs calculada y tasa de falsos hits (evaluada con golden set)
- C) Nº de vectores
- D) Uptime

**P6.** Vector Search encaja aquí como:
- A) Generador de respuestas
- B) Índice de similitud que encuentra la pregunta cacheada más cercana a la nueva
- C) Base de datos relacional
- D) LLM

---

## Soluciones
1. **B** — Equivalencia semántica, no coincidencia literal.
2. **B** — El umbral ES el sistema: calibrarlo con datos es el trabajo.
3. **B** — Falso hit = error silencioso servido con confianza.
4. **B** — Frescura heredada: invalidar al cambiar la fuente.
5. **B** — Ahorro + calidad medidos juntos.
6. **B** — El índice vectorial es el buscador de equivalencias.
