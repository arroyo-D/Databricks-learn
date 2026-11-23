# Ejercicio 5 — Spark Structured Streaming

## Objetivo
Construir tu primer pipeline de streaming: leer archivos JSON como stream, transformar y escribir en Delta con checkpoint.


## Por qué importa

Structured Streaming reutiliza la misma API de DataFrame sobre datos que no paran de llegar — y es donde el examen mezcla ingesta (**21%**) con troubleshooting (**10%**): triggers, checkpoints, watermarks y el manejo de datos tardíos. Conceptualmente es el salto más importante: dejar de pensar en "tablas" para pensar en "flujos".

## Conceptos
- Un stream es una **tabla sin límites**: cada micro-batch añade filas
- **Checkpoint**: guarda el progreso para recuperación exactamente-una-vez
- **Triggers**: processingTime, once, availableNow
- **Output modes**: append, update, complete

## Tareas

### 1. Stream básico (`locations_stream`)
- [ ] Crear carpeta de entrada en el volumen y subir el primer JSON de locations
- [ ] `spark.readStream` con schema explícito y `maxFilesPerTrigger = 1`
- [ ] Añadir columna `ingestion_date` con `current_timestamp()`
- [ ] `writeStream` a tabla Delta `zenviro.bronze.locations_stream` con checkpointLocation
- [ ] Subir más archivos y observar cómo crece la tabla

### 2. Tarea del curso
- [ ] Repetir con `departments` → `departments_stream`

### 3. Triggers
- [ ] Ejecutar con `.trigger(once=True)` y con `.trigger(availableNow=True)`
- [ ] Ejecutar con `processingTime='10 seconds'`

### 4. Output modes
- [ ] Probar `append` con una tabla destino
- [ ] Probar `complete` con una agregación (`groupBy().count()`)
- [ ] Explicar cuándo falla `complete` y cuándo es obligatorio `update`

### 5. Checkpoint
- [ ] Borrar el checkpoint y volver a ejecutar: ¿qué pasa con los datos?
- [ ] Cambiar el código sin borrar el checkpoint: ¿qué error aparece?

## Criterios de éxito

- [ ] Stream con checkpoint que sobrevive a un reinicio sin duplicar datos.
- [ ] Watermark configurado y explicado (qué pasa con un evento que llega tarde).
- [ ] Diferencia entre append, update y complete mode con ejemplo propio.

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Data engineering* → Structured Streaming)

## Entregable
Notebook `notebooks/05_streaming.py` + breve nota en `docs/output_modes.md`.