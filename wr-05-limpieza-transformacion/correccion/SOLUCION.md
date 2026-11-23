# Solución — WR-05

```python
from pyspark.sql import functions as F

# 1. Subconjuntos
df = spark.table("main.raw.viajes")
finde = df.filter(F.dayofweek("fecha").isin(1, 7))
laborable = df.subtract(finde)
rango = df.filter(F.col("fecha").between("2026-01-01", "2026-06-30"))

# 2. Regex
emails_ok = df.filter(F.col("email").rlike(r"^[\w.+-]+@[\w-]+\.[\w.]+$"))
df2 = (df
  .withColumn("cp", F.regexp_extract("direccion", r"(\d{5})", 1))
  .withColumn("telefono", F.regexp_replace("telefono", r"\D", "")))

# 3. Fechas
df_f = df.withColumn("fecha_ok", F.coalesce(
    F.to_date("fecha_txt", "yyyy-MM-dd"),
    F.to_date("fecha_txt", "dd/MM/yyyy"),
    F.to_date("fecha_txt", "MM-dd-yyyy")))

# Fechas Excel: días desde 1899-12-30
df_x = df.withColumn("fecha_real",
    F.date_add(F.lit("1899-12-30").cast("date"), F.col("excel_serial").cast("int")))

df_f = df_f.withColumns({
    "anio": F.year("fecha_ok"),
    "mes": F.month("fecha_ok"),
    "dia_semana": F.date_format("fecha_ok", "EEEE"),
})

# 4. De-cruft y dedup
ciudad_map = {"MADRID": "Madrid", "madrid": "Madrid", "MADRÍD": "Madrid"}
map_expr = F.create_map(*[F.lit(x) for kv in ciudad_map.items() for x in kv])
df_c = df.withColumn("ciudad", F.trim(F.col("ciudad"))) \
         .withColumn("ciudad", F.coalesce(map_expr[F.col("ciudad")], F.initcap("ciudad")))

from pyspark.sql.window import Window
w = Window.partitionBy("id").orderBy(F.desc("fecha_ok"))
df_dedup = (df_c.withColumn("rn", F.row_number().over(w))
            .filter("rn = 1").drop("rn"))
# alternativa simple: df.dropDuplicates(["id"])

# 6. Augmentación
df_aug = (df_dedup
    .join(F.broadcast(provincias), "cp_prefix", "left")
    .withColumn("es_finde", F.dayofweek("fecha_ok").isin(1, 7))
    .withColumn("franja", F.when(F.hour("ts") < 12, "mañana")
                           .when(F.hour("ts") < 20, "tarde").otherwise("noche")))
```

## Gotchas típicos
1. Nulo ≠ cadena vacía ≠ espacios: normaliza con `nullif(trim(col), '')`.
2. `inferSchema` + fechas: Spark no adivina formatos raros — parsea explícito.
3. `join` con duplicados en la dimensión multiplica filas: deduplica la dimensión primero.

## Puntos clave
- `coalesce` + varios formatos = parseo de fechas robusto.
- Window + `row_number` = dedup conservando "la mejor" fila.
- `broadcast` en joins con tablas pequeñas acelera mucho.
