# Solución — Ejercicio 4

```python
from pyspark.sql import functions as F

BASE = "/Volumes/zenviro/raw/data"

# 1. Lecturas
df_locations = spark.read.json(f"{BASE}/locations")
df_attend = (spark.read.option("sep", "\t").option("header", True)
             .csv(f"{BASE}/attendances"))
df_benefits = spark.read.parquet(f"{BASE}/benefits")
df_photos = (spark.read.format("binaryFile")
             .option("pathGlobFilter", "*.png")
             .load(f"{BASE}/photochecks"))

# 2. Transformaciones
df_bronze_loc = (df_locations
    .select(F.col("location_id").cast("int"),
            F.initcap("city").alias("city"),
            F.upper("country").alias("country"))
    .withColumn("ingestion_date", F.current_timestamp())
    .filter(F.col("location_id").isNotNull())
    .distinct())

# from_json + explode
schema = "employee_id INT, name STRING, skills ARRAY<STRING>"
df_emp = (spark.read.text(f"{BASE}/employees")
          .select(F.from_json("value", schema).alias("j"))
          .select("j.*"))
df_skills = df_emp.select("employee_id", F.explode("skills").alias("skill"))

# 3. Tablas
(df_bronze_loc.write.format("delta").mode("overwrite")
 .saveAsTable("zenviro.bronze.locations_py"))

# 4. Joins y agregados
df_join = (df_emp.join(df_bronze_loc, "location_id", "left")  # inner/left/left_semi/left_anti
           .groupBy("city")
           .agg(F.countDistinct("employee_id").alias("empleados"))
           .orderBy(F.desc("empleados")))

df_a.unionByName(df_b)          # union por nombre de columna
df_a.intersect(df_b)            # filas comunes (sin duplicados)
df_a.exceptAll(df_b)            # diferencia conservando duplicados

# 5. Vista temporal + SQL
df_emp.createOrReplaceTempView("tv_emp_py")
spark.sql("SELECT COUNT(*) FROM tv_emp_py").show()
```

## Puntos de examen
- `union` por posición vs `unionByName` por nombre.
- `left_semi` = filtra; `left_anti` = excluye; `intersect`/`exceptAll` para conjuntos.
- `saveAsTable` crea managed table; para externa usa `.option("path", ...)`.
- `display(df)` en vez de `df.show()` en notebooks Databricks.
