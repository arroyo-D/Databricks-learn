# cap-06 · Smart City: pipeline de streaming en tiempo real con Kafka + Databricks

> **Proyecto integrador** — adaptación del vídeo *Smart City End-to-End Realtime Data Streaming Pipeline* (Code with Yu) a Databricks. El vídeo usa Kafka + Spark + AWS (S3/Glue/Athena/Redshift) + PowerBI; aquí todo el lado analítico vive en Databricks, y Kafka corre en **tu servidor** (o en Docker local).
> Une lo aprendido en `dbx-05`, `dbx-06`, `dbx-07`, `dbx-08`, `dbx-09`, `dbx-10` y hermana con `cap-01`.

**Nivel:** 🔴 · **Duración estimada:** 2–3 semanas (2–3 h/día)
**Prerrequisitos:** `cap-01` recomendado · un Kafka accesible (tu servidor o Docker local)

## El escenario

Una ciudad inteligente emite telemetría continua de 5 fuentes IoT (igual que en el vídeo):

| Generador | Datos | Frecuencia |
|---|---|---|
| 🚗 Vehículos | matrícula, modelo, conductor | evento por trayecto |
| 📍 GPS | lat/lon, velocidad, dirección | 1 evento/seg/vehículo |
| 🚦 Tráfico | cámara, nº vehículos, incidencias | cada 5 s |
| 🌦️ Meteorología | temperatura, lluvia, viento, AQI | cada 10 s |
| 🚨 Emergencias | tipo, ubicación, severidad | aleatorio (raro) |

Objetivo: ingerir todo en tiempo real, construir la visión operativa de la ciudad y alertar emergencias en segundos.

## Del stack del vídeo a Databricks

| Vídeo (Code with Yu) | Aquí |
|---|---|
| Zookeeper + Kafka en Docker | **Kafka en tu servidor** (o Docker Compose local) |
| Spark Structured Streaming (cluster propio) | **Structured Streaming en Databricks** (misma API: `readStream.format("kafka")`) |
| AWS S3 | **Unity Catalog Volumes** + Delta |
| AWS Glue Crawlers + Athena | **Unity Catalog** (esquema gobernado) + Databricks SQL |
| AWS Redshift | Tablas **Gold Delta** + SQL Warehouse |
| PowerBI | **AI/BI Dashboards** + Genie |
| IAM policies | Permisos de Unity Catalog |

## Arquitectura objetivo

```
generadores IoT (Python) ──► Kafka (tu servidor)
   topics: vehicles, gps, traffic, weather, emergencies
                                   │
                                   ▼
        Databricks Structured Streaming (readStream kafka)
                                   │
              bronze_vehicles / bronze_gps / bronze_traffic / bronze_weather / bronze_emergencies
                                   │
                                   ▼  silver: parseo, tipado, dedupe, watermarks
              silver_gps (ventanas), silver_traffic, silver_weather, silver_emergencies
                                   │
                                   ▼  gold: KPIs por zona/ventana
              gold_trafico_zona_5min · gold_clima_actual · gold_emergencias_activas
                                   │
              ├─► AI/BI Dashboard (mapa de la ciudad en vivo)
              ├─► Alerta SQL: emergencia severidad alta → webhook
              └─► Genie: "¿qué zona tiene más tráfico ahora mismo?"
```

## Fases

### Fase 1 — Generadores IoT (días 1–3)

- [ ] 5 productores Python (uno por fuente) con datos sintéticos realistas: rutas coherentes entre vehículo y su GPS, clima correlacionado con tráfico (lluvia → más congestión).
- [ ] Cada productor publica JSON a su topic de Kafka con clave (`vehicle_id`, `zone_id`).
- [ ] Docker Compose de referencia en el repo (generadores; Kafka solo si lo quieres local).

### Fase 2 — Kafka en tu servidor (días 3–5)

- [ ] Kafka + Zookeeper desplegado en tu servidor (Docker Compose o instalación nativa).
- [ ] Topics creados con 3 particiones: `vehicles`, `gps`, `traffic`, `weather`, `emergencies`.
- [ ] Seguridad: SASL/TLS si el servidor es accesible por internet (ver ⚠️ Seguridad).
- [ ] Test: `kafka-console-producer/consumer` ida y vuelta.
- [ ] **Alternativa sin servidor**: generadores escribiendo JSON a un Volume + Auto Loader (patrón de `cap-01`). Mismo aprendizaje de streaming, cero red.

### Fase 3 — Bronze: ingesta streaming (días 5–8)

- [ ] Un stream por topic: `readStream.format("kafka")` con `checkpointLocation` propio y `failOnDataLoss=false`.
- [ ] Payload crudo a string + metadatos Kafka (topic, partition, offset, timestamp) en Bronze.
- [ ] Manejo de esquema como en el vídeo (sección *Fixing Schema Issues*): esquema explícito con `from_json`, columna `_rescued` para payloads rotos.

### Fase 4 — Silver: streams con estado (días 8–12)

- [ ] `silver_gps`: parseo, tipos, dedupe por `vehicle_id + event_ts`, **watermark** de 2 min.
- [ ] `silver_traffic`: join stream-stream GPS↔tráfico por zona y ventana (10 min de tolerancia).
- [ ] `silver_weather`: última lectura por estación (`foreachBatch` + MERGE o agregación con watermark).
- [ ] `silver_emergencies`: enriquecimiento con zona geográfica (UDF de geohash o join con tabla de zonas).

### Fase 5 — Gold + consumo (días 12–16)

- [ ] `gold_trafico_zona_5min`: vehículos/ventana, velocidad media, nivel de congestión por zona.
- [ ] `gold_emergencias_activas`: emergencias abiertas con su zona, severidad y tiempo transcurrido.
- [ ] Dashboard "ciudad en vivo": mapa (lat/lon de GPS), congestión por zona, clima actual, emergencias.
- [ ] SQL Alert: emergencia de severidad ≥ alta → webhook/email en <1 min desde el evento.
- [ ] Genie sobre gold con 5 preguntas de negocio preparadas.

### Fase 6 — Operación y pulido (días 16–18)

- [ ] Job orquestando los streams con reintentos y notificación de fallo.
- [ ] Monitorización del lag de consumo por topic (método en la solución) en el dashboard.
- [ ] Demo: lanza los 5 generadores, observa bronze→gold en vivo, provoca una emergencia y mira saltar la alerta.
- [ ] README final con diagrama y decisiones (por qué watermark de 2 min, por qué 3 particiones...).

## Referencias del vídeo (apoyo visual)

- Vídeo: https://www.youtube.com/watch?v=Vv_fvwF41_0 — capítulos: arquitectura (1:29), Docker setup (9:00), generadores IoT (26:17–1:03:39), producir a Kafka (1:03:39), Spark streaming desde Kafka (1:19:14), **schema issues en streaming (2:01:14)**, catálogo y serving (2:07:31+).
- Docs Kafka: https://kafka.apache.org/ · Docs Databricks: https://docs.databricks.com/ (*Data engineering* → Structured Streaming → Kafka).

## Criterios de éxito

- [ ] Un evento GPS publicado en Kafka aparece en gold en <30 s.
- [ ] El stream sobrevive a un reinicio sin duplicados (checkpoint).
- [ ] Una emergencia simulada dispara la alerta en <1 min.
- [ ] Lag de consumo visible y explicable en el dashboard.

## ⚠️ Seguridad

- **Tu servidor Kafka en internet**: usa SASL_SSL + ACLs, nunca PLAINTEXT abierto. Credenciales en Databricks Secrets (`dbutils.secrets.get`), nunca en notebooks.
- Si usas Databricks Free Edition (serverless), verifica que tu Kafka es alcanzable desde internet; si no puedes exponerlo, usa la alternativa Auto Loader del punto 2 — mismo aprendizaje.
- Datos 100% sintéticos.
