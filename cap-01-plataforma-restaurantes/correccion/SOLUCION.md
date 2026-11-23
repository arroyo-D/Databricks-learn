# Solución de referencia — cap-01 · Plataforma de restaurantes

> ⚠️ Este capstone integra módulos que ya tienen solución detallada. Aquí van los puntos de unión y las decisiones; el código granular está en `dbx-05`, `dbx-06`, `dbx-08`, `dbx-09`, `dbx-10`.

## Decisiones de arquitectura

1. **Generador como fuente streaming**: si no tienes Azure Event Hub, el patrón "notebook que escribe JSON a un Volume + Auto Loader" reproduce exactamente la misma mecánica de streaming (micro-batches, checkpoints, schema evolution). Si tienes Azure, cambia solo el `readStream`.

```python
(spark.readStream.format("cloudFiles")
   .option("cloudFiles.format", "json")
   .option("cloudFiles.schemaLocation", f"{CKPT}/schema")
   .load(SRC)
   .writeStream.option("checkpointLocation", f"{CKPT}/orders")
   .trigger(availableNow=True)   # o processingTime='30 seconds'
   .toTable("cap_restaurantes.bronze.orders"))
```

2. **CDC con APPLY CHANGES**: la clave es una columna de secuencia (`updated_at`) y claves estables:

```sql
APPLY CHANGES INTO cap_restaurantes.silver.empleados
FROM stream(cap_restaurantes.bronze.empleados_cdc)
KEYS (empleado_id)
APPLY AS DELETE WHEN op = 'DELETE'
SEQUENCE BY updated_at
STORED AS SCD TYPE 2;
```

3. **Sentimiento dentro del pipeline**, no fuera: la silver de reseñas llama a la IA como una transformación más:

```sql
SELECT review_id, texto,
  ai_analyze_sentiment(texto) AS sentimiento,
  ai_query('databricks-meta-llama-3-3-70b-instruct',
    concat('Extrae 1-3 temas de esta reseña, separados por coma: ', texto)) AS temas
FROM cap_restaurantes.bronze.reviews
```

Ojo al coste: `ai_query` por fila escala en precio. Filtra/deduplica antes (reseñas nuevas solamente, no todo el histórico en cada run).

4. **Expectations con quarantine**: `@dlt.expect_or_drop("importe_positivo", "importe > 0")` en silver y tabla de cuarentena para lo peor — la observabilidad del pipeline la da SDP gratis (event log).

5. **Job final**: task `ingesta_batch` → task `pipeline_sdp` → task `refresh_dashboard` con `if/else` sobre el estado y notificación en fallo.

## Errores comunes

- Recomputar el sentimiento de todas las reseñas en cada ejecución (coste innecesario): incremental por `review_id`.
- Gold sin granularidad definida: decide el grano (restaurante × hora) antes de escribir una línea.
- Streaming con `processingTime` muy agresivo en desarrollo: gasta recursos; usa `availableNow` mientras pruebas.

## Cómo validarlo

1. Latencia punta a punta medida: `gold.max(ts) - pedido.ts` < 2 min.
2. Cambia el nombre de un restaurante en el maestro → el gold lo refleja (SCD2 en silver conserva el histórico).
3. Dashboard sin refresco manual tras el run del job.
