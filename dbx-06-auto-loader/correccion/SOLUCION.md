# Solución — Ejercicio 6

```python
BASE = "/Volumes/zenviro/raw/streaming"

# 1. Auto Loader básico
stream_df = (spark.readStream
    .format("cloudFiles")
    .option("cloudFiles.format", "json")
    .option("cloudFiles.schemaLocation", f"{BASE}/checkpoints/schema_locations_al")
    .option("cloudFiles.inferColumnTypes", "true")
    .load(f"{BASE}/locations_in"))

query = (stream_df.writeStream
    .format("delta")
    .option("checkpointLocation", f"{BASE}/checkpoints/locations_autoloader")
    .trigger(availableNow=True)
    .toTable("zenviro.bronze.locations_autoloader"))
```

## Schema evolution

```python
# addNewColumns: añade columnas nuevas al schema y reintenta el batch
.option("cloudFiles.schemaEvolutionMode", "addNewColumns")

# rescue: columnas nuevas van a _rescued_data (no se añaden)
.option("cloudFiles.schemaEvolutionMode", "rescue")

# failOnNewColumns: el stream falla -> ideal para detectar cambios inesperados
.option("cloudFiles.schemaEvolutionMode", "failOnNewColumns")

# none: ignora las columnas nuevas silenciosamente
.option("cloudFiles.schemaEvolutionMode", "none")
```

Consultar datos rescatados:
```sql
SELECT _rescued_data FROM zenviro.bronze.locations_autoloader
WHERE _rescued_data IS NOT NULL;
```

## Directory listing vs File notification
| Modo | Cómo funciona | Cuándo |
|---|---|---|
| Directory listing | Lista el directorio y compara con lo ya procesado | Por defecto; pocos archivos |
| File notification | Cola de eventos del storage (SNS/SQS, Event Grid...) | Millones de archivos, alta frecuencia |

## Puntos de examen
- Auto Loader procesa cada archivo **exactamente una vez** (estado en schemaLocation/checkpoint).
- `_rescued_data` evita perder datos que no encajan en el schema.
- El schema se infiere UNA vez al arrancar; la evolución se gestiona con schemaEvolutionMode.
