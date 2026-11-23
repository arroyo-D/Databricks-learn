# Solución — WR-02

## CSV de ejemplo (`data/ventas_sucias.csv`)
```csv
id,ciudad,email,edad,importe,fecha
1,Madrid,ana@x.com,34,120.5,2026-01-05
2,madrid,bad-email,-5,80,2026-01-05
3,MADRID,luis@x.com,200,0,2030-12-31
4,Barcelona,,45,60,2026-02-01
4,Barcelona,,45,60,2026-02-01
```

## 1. Checklist con PySpark
```python
from pyspark.sql import functions as F

df = spark.read.option("header", True).csv("/Volumes/main/raw/wr/ventas_sucias.csv")

# Completitud
total = df.count()
for c in df.columns:
    nulos = df.filter(F.col(c).isNull()).count()
    print(f"{c}: {nulos/total:.1%} nulos")

# Consistencia
df.groupBy("ciudad").count().show()          # Madrid / madrid / MADRID
df.groupBy(F.upper("ciudad")).count().show()  # versión normalizada

# Validez de dominio
df.filter(F.col("edad").cast("int") < 0).show()
df.filter(~F.col("email").contains("@")).show()
df.filter(F.col("importe").cast("double") < 0).show()

# Unicidad
df.groupBy("id").count().filter("count > 1").show()

# Actualidad
df.filter(F.to_date("fecha") > F.current_date()).show()
```

## 2. Profiling
```python
df.describe().show()                      # count, mean, stddev, min, max
df.summary().show()                       # + percentiles
for c in df.columns:
    print(c, df.select(c).distinct().count())
df.printSchema()  # ojo: CSV todo STRING -> casteos obligatorios
```

## 3. Veredicto fit
Para "ventas por ciudad al mes": el dataset NO es fit sin limpieza — duplicados por id, ciudad inconsistente y un importe negativo. Tras deduplicar, normalizar `upper(ciudad)` y filtrar importes/fechas inválidos, sí.

## 4. Expectations
```sql
CONSTRAINT id_unico  EXPECT (id IS NOT NULL) ON VIOLATION DROP ROW
CONSTRAINT importe_ok EXPECT (importe >= 0) ON VIOLATION DROP ROW
CONSTRAINT email_ok   EXPECT (email RLIKE '^[^@]+@[^@]+\\.[^@]+$')
```

## Puntos clave
- Integridad ≠ fit: un dato correcto puede no servir para tu pregunta (representatividad).
- Perfil primero, limpiar después: mide antes de tocar.
- Las expectativas convierten hallazgos en reglas permanentes del pipeline.
