# Solución de referencia — cap-06 · Smart City con Kafka

> ⚠️ Intenta el proyecto antes de leer esto. Cubre los puntos donde más gente se atasca: opciones de Kafka en Spark, esquemas, watermarks y monitorización de lag.

## 1. Conexión Kafka → Databricks

```python
KAFKA_OPTS = {
    "kafka.bootstrap.servers": dbutils.secrets.get("kafka", "bootstrap_servers"),
    "kafka.security.protocol": "SASL_SSL",
    "kafka.sasl.mechanism": "PLAIN",
    "kafka.sasl.jaas.config":
        f'org.apache.kafka.common.security.plain.PlainLoginModule required '
        f'username="{dbutils.secrets.get("kafka","user")}" '
        f'password="{dbutils.secrets.get("kafka","pass")}";',
    "subscribe": "gps",
    "startingOffsets": "latest",
    "failOnDataLoss": "false",
}

raw = (spark.readStream.format("kafka").options(**KAFKA_OPTS).load()
       .selectExpr("CAST(value AS STRING) AS payload", "topic", "partition",
                   "offset", "timestamp AS kafka_ts"))
```

Los brokers **no** van en el código: scope `kafka` en Databricks Secrets. `failOnDataLoss=false` evita que el stream muera si Kafka purga offsets antiguos (documéntalo: en producción decidirías esto con criterio de negocio).

## 2. Esquema explícito (la lección del minuto 2:01 del vídeo)

```python
from pyspark.sql.types import *
GPS_SCHEMA = StructType([
    StructField("vehicle_id", StringType()),
    StructField("lat", DoubleType()),
    StructField("lon", DoubleType()),
    StructField("speed_kmh", DoubleType()),
    StructField("event_ts", TimestampType()),
])

silver_gps = (raw
    .select(from_json("payload", GPS_SCHEMA,
                      {"mode": "PERMISSIVE", "columnNameOfCorruptRecord": "_rescued"}).alias("d"),
            "kafka_ts")
    .select("d.*", "kafka_ts")
    .withWatermark("event_ts", "2 minutes")
    .dropDuplicates(["vehicle_id", "event_ts"]))
```

Sin esquema explícito, Spark infiere todo string (o falla silenciosamente): este es EL error del proyecto, igual que en el vídeo.

## 3. Join stream-stream con watermarks

```python
joined = (silver_gps.alias("g")
    .join(silver_traffic.alias("t"),
          expr("""g.zone_id = t.zone_id AND
                  t.event_ts BETWEEN g.event_ts - INTERVAL 10 MINUTES
                                 AND g.event_ts + INTERVAL 10 MINUTES""")))
```

Regla: **ambos** lados del join necesitan watermark y el join necesita restricción temporal, o el estado crece para siempre.

## 4. Gold por ventanas

```python
(gps_con_zona
   .groupBy(window("event_ts", "5 minutes"), "zone_id")
   .agg(count("*").alias("vehiculos"),
        avg("speed_kmh").alias("vel_media"))
   .withColumn("congestion",
        when(col("vel_media") < 20, "alta")
        .when(col("vel_media") < 40, "media").otherwise("baja"))
   .writeStream.outputMode("update")
   .option("checkpointLocation", f"{CKPT}/gold_trafico")
   .trigger(availableNow=True)     # o processingTime="30 seconds" en producción
   .toTable("cap_smartcity.gold.trafico_zona_5min"))
```

## 5. Monitorización del lag

```python
# último offset consumido vs último offset disponible por partición
# opción simple: último kafka_ts ingerido por topic
(spark.table("cap_smartcity.bronze.gps")
   .groupBy("partition").agg(max("kafka_ts").alias("ultimo_evento"),
                             max("offset").alias("ultimo_offset")))
# compara con los offsets finales del topic (AdminClient o kafka-get-offsets)
```

Si el lag crece de forma sostenida: el stream no da abasto → más recursos o menos estado.

## Errores comunes

- `startingOffsets=earliest` + topic con historia: el primer run procesa TODO (tardas horas). En desarrollo, `latest`.
- Watermark olvidado en uno de los lados del join: estado infinito → OOM.
- Checkpoint compartido entre dos queries: cada query necesita el suyo propio.
- Kafka en PLAINTEXT expuesto a internet: cualquiera puede producir/consumir. SASL_SSL siempre.

## Cómo validarlo

1. Publica 10 eventos GPS con `kafka-console-producer` → aparecen en bronze en segundos.
2. Mata el stream, publica 5 más, rearranca → esos 5 se procesan, sin duplicados.
3. Genera una emergencia severidad 5 → alerta recibida en <1 min.
