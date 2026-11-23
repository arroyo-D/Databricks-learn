# Preguntas de práctica — dbx-03 / Dominio: Data Transformation & Modeling (22%)

**P1.** Para convertir una columna STRING que contiene JSON en columnas estructuradas:
- A) `to_json()`
- B) `from_json()` con un schema
- C) `parse_json()` sin schema
- D) `json_extract()`

**P2.** `schema_of_json()` devuelve:
- A) El JSON validado
- B) El DDL del schema inferido a partir de un ejemplo
- C) Las tablas del catálogo
- D) El schema de una tabla Delta

**P3.** `explode(skills)` sobre un ARRAY produce:
- A) Un string concatenado
- B) Una fila por elemento del array
- C) Un nuevo array ordenado
- D) Un error si el array está vacío

**P4.** ¿Cuál NO es una función de orden superior?
- A) `transform`
- B) `filter`
- C) `exists`
- D) `concat_ws`

**P5.** Una UDF en Spark SQL respecto a funciones nativas:
- A) Siempre es más rápida
- B) Suele ser más lenta; mejor usar funciones nativas cuando existan
- C) No puede registrarse en Unity Catalog
- D) Solo funciona en Scala

**P6.** En la arquitectura Medallón, la capa Silver contiene:
- A) Datos crudos sin tocar
- B) Datos limpios, conformados y modelados
- C) Solo agregados de negocio
- D) Copias de seguridad

**P7.** `INSERT OVERWRITE` frente a `INSERT INTO`:
- A) Son equivalentes
- B) OVERWRITE reemplaza los datos/particiones; INTO añade filas
- C) OVERWRITE solo funciona con Parquet
- D) INTO no existe en Spark SQL

---

## Soluciones
1. **B** — `from_json(col, schema)`; `to_json` hace lo contrario.
2. **B** — Muy útil para derivar el schema de un JSON de ejemplo.
3. **B** — Explode aplana arrays a filas (un elemento por fila).
4. **D** — `concat_ws` es función de strings normal; las de orden superior toman lambdas `x -> ...`.
5. **B** — Las UDFs rompen optimizaciones del Catalyst; prioriza funciones nativas/orden superior.
6. **B** — Bronze=crudo, Silver=limpio/conformado, Gold=agregado.
7. **B** — Ojo: OVERWRITE sustituye; INTO apendiza.
