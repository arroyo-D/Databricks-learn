# Preguntas de práctica — wr-05 / Limpieza y transformación

**P1.** Ante un 15% de nulos en una columna numérica clave, lo primero:
- A) Rellenar con la media siempre
- B) Entender POR QUÉ faltan (¿aleatorio? ¿sistemático?) antes de decidir imputar, eliminar o modelar el ausente
- C) Borrar la columna
- D) Poner 0

**P2.** Duplicados exactos vs duplicados de entidad (misma persona, distinto formato):
- A) Se tratan igual
- B) Los exactos con `dropDuplicates()`; los de entidad requieren normalización (case, tildes, trim) y reglas de matching
- C) Los de entidad no existen
- D) Solo con SQL

**P3.** Normalizar fechas "03/04/2025" ambiguas (¿3 de abril o 4 de marzo?):
- A) Asumir formato USA
- B) Definir el formato esperado, parsear con formato explícito y auditar los fallos
- C) Dejarlas como string
- D) Usar regex genérico

**P4.** Outliers: un precio de -50 € es; un sueldo de 500.000 € en el dataset es:
- A) Ambos errores
- B) El primero inválido (regla de negocio); el segundo, un valor extremo a investigar, no a borrar automáticamente
- C) Ambos válidos
- D) Ambos se recortan al percentil 99

**P5.** Estandarizar texto categórico ("Madrid", "madrid ", "MADRID"):
- A) `upper(trim(...))` + tabla de equivalencias documentada
- B) Confianza en el origen
- C) Regex por cada caso
- D) Hash de la columna

**P6.** La regla de oro entre Bronze y Silver:
- A) Bronze se limpia a fondo
- B) Bronze inmutable (crudo); TODA la limpieza vive en transformaciones reproducibles hacia Silver
- C) Silver guarda copias del crudo
- D) Da igual mientras funcione

**P7.** Una transformación idempotente significa:
- A) Que es rápida
- B) Que reejecutarla con los mismos datos de entrada produce el mismo resultado sin duplicar ni corromper
- C) Que no usa JOINs
- D) Que corre en streaming

**P8.** Para unir datasets con claves inconsistentes (provincias con tildes y mayúsculas distintas):
- A) JOIN directo
- B) Columna de join normalizada en ambos lados + verificación del % de filas emparejadas
- C) JOIN por posición de fila
- D) Fuzzy join siempre

---

## Soluciones
1. **B** — El mecanismo del missing determina el tratamiento.
2. **B** — Dos problemas distintos con herramientas distintas.
3. **B** — Formato explícito + auditoría de parseos fallidos.
4. **B** — Inválido (imposible) ≠ extremo (posible pero raro).
5. **A** — Normalización + equivalencias: reproducible y auditable.
6. **B** — La limpieza es código reproducible, no edición del crudo.
7. **B** — Idempotencia = reejecución segura (MERGE, overwrite por partición...).
8. **B** — Normalizar claves y medir el match rate.
