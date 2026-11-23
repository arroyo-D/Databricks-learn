# Ejercicio 4 — PySpark: el módulo SQL en Python

## Objetivo
Rehacer las lecturas y transformaciones del proyecto Zenviro usando la API de DataFrames de PySpark.

## Tareas

### 1. Lecturas
- [ ] `spark.read.json("<ruta>")` para locations/departments
- [ ] `spark.read.option("sep", "\t").csv(...)` para TSV
- [ ] `spark.read.parquet(...)` para benefits
- [ ] `spark.read.format("binaryFile")...` para imágenes

### 2. Transformaciones
- [ ] `select`, `withColumn`, `withColumnRenamed`, `filter`, `distinct`
- [ ] Cast de tipos con `col().cast()`
- [ ] Fecha de ingesta con `current_timestamp()`
- [ ] Parseo de JSON: `from_json(col, schema)` y `explode`

### 3. Tablas
- [ ] Crear `employment_histories_py` con `df.write.saveAsTable()`
- [ ] Leer trainings (CSV) y benefits (Parquet) como archivos externos y guardarlos en `zenviro.bronze` con sufijo `_py`

### 4. Joins y agregados en Python
- [ ] `df.join(...)` con tipos: inner, left, semi, anti
- [ ] `groupBy().agg()` y funciones de `pyspark.sql.functions`
- [ ] `union` / `unionByName`, `intersect`, `exceptAll`

### 5. Spark SQL desde Python
- [ ] Crear vista temporal con `createOrReplaceTempView`
- [ ] Consultarla con `spark.sql("SELECT ...")`

## Entregable
Notebook `notebooks/04_pyspark_zenviro.py` con todo ejecutado.
