# Solución — WR-07

```python
from pyspark.sql import functions as F

df = spark.table("samples.nyctaxi.trips")  # dataset de muestra integrado

# 2. Tendencia central
stats = df.select(
    F.mean("trip_distance").alias("media"),
    F.percentile_approx("trip_distance", 0.5).alias("mediana"),
    F.stddev("trip_distance").alias("std"),
).first()
# media >> mediana => cola derecha (outliers largos)

# 3. Outliers IQR
q1, q3 = df.approxQuantile("trip_distance", [0.25, 0.75], 0.01)
iqr = q3 - q1
lo, hi = q1 - 1.5*iqr, q3 + 1.5*iqr
outliers = df.filter((F.col("trip_distance") < lo) | (F.col("trip_distance") > hi))
print(f"{outliers.count()/df.count():.2%} outliers por IQR")

# z-score
z = df.withColumn("z", (F.col("trip_distance") - stats.media) / stats.std)
z.filter(F.abs("z") > 3).count()

# 4. Distribución
display(df.select("trip_distance"))  # en la UI: Plot -> Histogram

# 5. Proporciones
df.groupBy("payment_type").count() \
  .withColumn("pct", F.round(F.col("count")/df.count()*100, 2)).show()

# 6. Pregunta de negocio: ¿qué franja horaria genera más ingreso por milla?
res = (df.withColumn("hora", F.hour("tpep_pickup_datetime"))
  .withColumn("franja", F.when(F.col("hora") < 12, "mañana")
                         .when(F.col("hora") < 20, "tarde").otherwise("noche"))
  .groupBy("franja")
  .agg((F.sum("fare_amount")/F.sum("trip_distance")).alias("usd_por_milla"),
       F.count("*").alias("viajes"))
  .orderBy(F.desc("usd_por_milla")))
display(res)
```

## Puntos clave
- Media sensible a outliers; mediana robusta. Compáralas siempre.
- IQR para distribuciones asimétricas; z-score asume normalidad.
- Los outliers no se borran: se investigan. Un outlier puede ser tu mejor hallazgo.
- EDA sin preguntas previas = mirar sin ver. Contexto primero.
