# Preguntas de práctica — cap-01 / Plataforma de restaurantes E2E

**P1.** La arquitectura medallón en este proyecto existe para:
- A) Cumplir una norma
- B) Separar crudo inmutable (bronze) → datos confiables limpios (silver) → métricas de negocio (gold), cada capa reprocesable desde la anterior
- C) Usar más storage
- D) Tener tres esquemas

**P2.** El generador de pedidos con picos y eventos tardíos sirve para:
- A) Decorar
- B) Probar el comportamiento real del streaming: watermarks, deduplicación y ventanas bajo desorden, no el camino feliz
- C) Llenar el disco
- D) Simular Black Friday solamente

**P3.** El checkpointLocation del stream garantiza:
- A) Velocidad
- B) Recuperación exactly-once: si el stream cae, reanuda donde quedó sin duplicar ni perder eventos
- C) Compresión
- D) Gobernanza

**P4.** Las dimensiones SCD tipo 2 (empleados, menús) conservan:
- A) Solo el valor actual
- B) El histórico de cambios con vigencias: poder responder "¿quién era el gerente de ese local EN MARZO?"
- C) Los borrados lógicos solamente
- D) Duplicados de todo

**P5.** El sentimiento de reseñas se calcula en Silver (no en el dashboard) porque:
- A) Es más rápido
- B) Es un dato transformado gobernado: se calcula una vez con `ai_query`, queda en Delta y lo consumen todos los productos
- C) AI/BI no puede
- D) Es más barato siempre

**P6.** El Genie space sobre Gold funciona bien porque:
- A) Genie es mágico
- B) Gold tiene nombres de negocio, PK/FK, COMMENTs y métricas pre-agregadas: la semántica que Genie necesita
- C) Tiene pocos datos
- D) Usa streaming

---

## Soluciones
1. **B** — Capas con contratos: cada una rehace a la siguiente.
2. **B** — El streaming se prueba con desorden, no con el camino feliz.
3. **B** — Checkpoint = reanudación exactly-once.
4. **B** — SCD2 = verdad histórica por vigencias.
5. **B** — Transformación gobernada una vez, consumo múltiple.
6. **B** — Semántica de Gold = Genie acierta.
