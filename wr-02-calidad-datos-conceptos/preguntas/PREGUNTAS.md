# Preguntas de práctica — wr-02 / Calidad de datos: conceptos

**P1.** Las 6 dimensiones clásicas de calidad de datos NO incluyen:
- A) Completitud
- B) Consistencia
- C) Compresión
- D) Validez

**P2.** Completitud mide:
- A) Que los valores sean correctos
- B) La proporción de valores presentes vs esperados (nulos/ausentes)
- C) Que no haya duplicados
- D) Que el tipo de dato sea el correcto

**P3.** Un campo `edad` con valor 340 falla en:
- A) Completitud
- B) Validez (rango/plausibilidad)
- C) Unicidad
- D) Actualidad

**P4.** La diferencia entre validación y verificación de datos:
- A) Son sinónimos
- B) Validación = cumple formato/reglas internas; verificación = corresponde con la realidad/fuente autorizada
- C) Validación es manual y verificación automática
- D) Validación solo aplica a texto

**P5.** Las "expectations" de SDP (`@dp.expect_or_drop`) implementan calidad como:
- A) Un informe PDF posterior
- B) Reglas declarativas en el propio pipeline que miden/descartan/fallan según severidad
- C) Un script externo semanal
- D) Permisos de Unity Catalog

**P6.** El patrón de cuarentena consiste en:
- A) Borrar las filas malas
- B) Desviar los registros inválidos a una tabla aparte, visible y reprocesable, sin perderlos
- C) Marcar con NULL los errores
- D) Parar el pipeline siempre

**P7.** Unicidad se rompe típicamente por:
- A) Valores fuera de rango
- B) Duplicados en la clave natural (mismo pedido ingerido dos veces)
- C) Campos nulos
- D) Codificación UTF-8

**P8.** Un "data contract" es:
- A) Un acuerdo legal con el proveedor de nube
- B) Un contrato explícito productor-consumidor: esquema, SLA, semántica y reglas de calidad esperadas
- C) Un trigger de base de datos
- D) Un tipo de tabla Delta

---

## Soluciones
1. **C** — Las 6: completitud, unicidad, validez, consistencia, exactitud, actualidad.
2. **B** — % de datos presentes frente a lo esperado.
3. **B** — Valores imposibles = validez/plausibilidad.
4. **B** — Distinción clave del libro: reglas internas vs correspondencia con la realidad.
5. **B** — Calidad declarativa dentro del pipeline, no como paso externo.
6. **B** — Visibilidad sin pérdida: patrón usado en dbx-09.
7. **B** — Duplicados de clave = fallo de unicidad.
8. **B** — Contrato de datos: expectativas formalizadas entre equipos.
