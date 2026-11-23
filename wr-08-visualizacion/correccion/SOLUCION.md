# Solución — WR-08

```python
import matplotlib.pyplot as plt
import seaborn as sns
from pyspark.sql import functions as F
sns.set_theme()

df = spark.table("samples.nyctaxi.trips")
pdf = df.sample(fraction=0.01).toPandas()  # ¡muestra! nunca toPandas() a lo bruto

# 1. Barras vs pie
agg = pdf["payment_type"].value_counts()
fig, axes = plt.subplots(1, 2, figsize=(12, 4))
agg.plot.pie(ax=axes[0], autopct="%.0f%%"); axes[0].set_ylabel("")
agg.plot.bar(ax=axes[1]); axes[1].set_ylabel("viajes")
display(fig)   # en Databricks: display(fig) renderiza matplotlib
# Ganador: barras — comparar longitudes es más preciso que comparar ángulos.

# Serie temporal
daily = (df.groupBy(F.to_date("tpep_pickup_datetime").alias("dia"))
         .count().orderBy("dia").toPandas())
fig, ax = plt.subplots(figsize=(10, 4))
ax.plot(daily["dia"], daily["count"]); ax.set_title("Viajes por día")
display(fig)

# Scatter
fig, ax = plt.subplots()
ax.scatter(pdf["trip_distance"], pdf["fare_amount"], alpha=0.1)
ax.set_xlabel("millas"); ax.set_ylabel("USD")
display(fig)

# 3. seaborn
fig, ax = plt.subplots()
sns.boxplot(data=pdf, x="payment_type", y="trip_distance", ax=ax)
display(fig)

fig, ax = plt.subplots()
sns.heatmap(pdf[["trip_distance","fare_amount","tip_amount"]].corr(),
            annot=True, cmap="vlag", ax=ax)
display(fig)

# 4. Anotación + colorblind
sns.set_palette("colorblind")
fig, ax = plt.subplots()
ax.hist(pdf["fare_amount"], bins=50)
ax.axvline(pdf["fare_amount"].median(), color="red", ls="--")
ax.annotate("mediana", xy=(pdf["fare_amount"].median(), 0))
display(fig)
```

## Eje truncado
Un eje Y que empieza en 90 en vez de 0 hace que una diferencia del 5% parezca del 500%. Regla: barras siempre desde 0; líneas pueden truncarse si se indica.

## Puntos clave
- Mensaje → forma: comparación=barras, evolución=línea, relación=scatter, distribución=histograma/boxplot.
- `display(fig)` integra matplotlib en Databricks; los plots nativos de `display(df)` son la vía rápida.
- Anota siempre el "para qué mirar aquí".
