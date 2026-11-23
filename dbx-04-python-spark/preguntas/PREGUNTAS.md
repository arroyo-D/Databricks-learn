# Preguntas de práctica — dbx-04 / Dominio: Data Transformation & Modeling (PySpark)

**P1.** `df_a.union(df_b)` combina columnas por:
- A) Nombre
- B) Posición
- C) Tipo
- D) Orden alfabético

**P2.** Para unir por nombre de columna aunque el orden difiera:
- A) `union`
- B) `unionByName`
- C) `join`
- D) `merge`

**P3.** Un join `left_semi` devuelve:
- A) Todas las filas de ambas tablas
- B) Filas de la izquierda que TIENEN coincidencia (sin columnas de la derecha)
- C) Filas de la izquierda sin coincidencia
- D) El producto cartesiano

**P4.** Un join `left_anti` devuelve:
- A) Filas de la izquierda SIN coincidencia en la derecha
- B) Filas duplicadas
- C) Solo las columnas comunes
- D) Todas las filas de la derecha

**P5.** `df.write.saveAsTable("cat.sch.tab")` sin option("path") crea:
- A) Tabla externa
- B) Tabla managed
- C) Vista temporal
- D) Vista materializada

**P6.** `df.createOrReplaceTempView("v")` + `spark.sql("SELECT * FROM v")`:
- A) Persiste la vista en el catálogo
- B) Crea una vista solo visible en la sesión Spark actual
- C) Falla: no se puede mezclar API y SQL
- D) Crea una vista global

**P7.** `intersect` vs `exceptAll`:
- A) intersect = filas comunes; exceptAll = diferencia conservando duplicados
- B) Son sinónimos
- C) intersect conserva duplicados siempre
- D) exceptAll solo funciona con tablas Delta

---

## Soluciones
1. **B** — `union` es posicional; peligro si el orden de columnas difiere.
2. **B** — `unionByName` une por nombre y permite columnas faltantes (con allowMissingColumns).
3. **B** — semi = filtro "existe en la derecha", sin duplicar ni traer columnas.
4. **A** — anti = "NOT IN": filas sin coincidencia.
5. **B** — Sin path externo, es managed (UC gestiona los datos).
6. **B** — Temp view = ámbito de sesión.
7. **A** — `except` elimina duplicados; `exceptAll` los conserva.
