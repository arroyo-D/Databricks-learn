# Ejercicio 4 — PySpark: el módulo SQL en Python

## Objetivo
Rehacer las lecturas y transformaciones del proyecto Zenviro usando la API de DataFrames de PySpark.


## Por qué importa

PySpark es la herramienta diaria del data engineer en Databricks y atraviesa todo el dominio de transformación (**22%** del examen). Este módulo te da fluidez con la DataFrame API: selects, filtros, joins, agregaciones, funciones de ventana y UDFs (y por qué evitarlas si hay función nativa). Sin esta fluidez, los módulos de streaming y pipelines se hacen cuesta arriba.

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

## Criterios de éxito

- [ ] Las 15 operaciones más comunes escritas sin consultar apuntes.
- [ ] Join con broadcast explicado: cuándo ayuda y por qué.
- [ ] Plan físico leído en el Spark UI (identificar un exchange/shuffle).

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Data engineering*, referencia PySpark)

## Entregable
Notebook `notebooks/04_pyspark_zenviro.py` con todo ejecutado.