# Solución — Ejercicio 5

```python
from pyspark.sql import functions as F
from pyspark.sql.types import StructType, StructField, IntegerType, StringType

BASE = "/Volumes/zenviro/raw/streaming"

schema = StructType([
    StructField("location_id", IntegerType()),
    StructField("city", StringType()),
    StructField("country", StringType()),
])

# 1. Lectura del stream
stream_df = (spark.readStream
    .format("json")
    .schema(schema)
    .option("maxFilesPerTrigger", 1)
    .load(f"{BASE}/locations_in"))

t_stream_df = stream_df.withColumn("ingestion_date", F.current_timestamp())

# Escritura a Delta con checkpoint
query = (t_stream_df.writeStream
    .format("delta")
    .option("checkpointLocation", f"{BASE}/checkpoints/locations_stream")
    .trigger(availableNow=True)
    .toTable("zenviro.bronze.locations_stream"))
```

## Triggers
- `trigger(once=True)`: procesa todo lo pendiente y para (deprecated a favor de availableNow).
- `trigger(availableNow=True)`: igual que once pero en varios micro-batches.
- `trigger(processingTime='10 seconds')`: micro-batch continuo cada 10 s.

## Output modes
| Modo | Uso |
|---|---|
| append | Solo filas nuevas (por defecto) |
| complete | Reescribe todo el resultado: solo con agregaciones |
| update | Solo filas actualizadas: agregaciones sin sobrescribir todo |

```python
# complete con agregación
agg = stream_df.groupBy("country").count()
q = (agg.writeStream.outputMode("complete")
     .option("checkpointLocation", f"{BASE}/checkpoints/agg")
     .toTable("zenviro.bronze.locations_by_country"))
```

## Checkpoint (respuestas)
- Borrar el checkpoint → el stream **relee todos los archivos** desde el principio (duplicados si no gestionas el destino).
- Cambiar la query sin nuevo checkpoint → error: cambios no permitidos en la query con el mismo checkpoint (salvo cambios compatibles).
- Nunca reutilices un checkpoint entre queries distintas.

## Puntos de examen
- `maxFilesPerTrigger` controla el ritmo de ingesta en pruebas.
- `availableNow` es la opción recomendada para batch incremental programado.
- Exactly-once = checkpoint + sink idempotente (Delta).
