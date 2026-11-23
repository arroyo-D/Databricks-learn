# Preguntas de práctica — dbx-06 / Dominio: Data Ingestion & Loading (Auto Loader)

**P1.** Auto Loader se activa con:
- A) `spark.read.format("autoloader")`
- B) `spark.readStream.format("cloudFiles")`
- C) `COPY INTO` con opción auto
- D) `dbutils.fs.ls` en bucle

**P2.** La opción `cloudFiles.schemaLocation` se usa para:
- A) Guardar los datos
- B) Almacenar el schema inferido y el progreso de inferencia
- C) Definir el formato de salida
- D) Configurar notificaciones

**P3.** `cloudFiles.schemaEvolutionMode = "rescue"`:
- A) El stream falla ante columnas nuevas
- B) Añade las columnas nuevas al schema automáticamente
- C) Las columnas nuevas/no esperadas van a la columna `_rescued_data`
- D) Ignora los archivos nuevos

**P4.** `failOnNewColumns` es útil cuando:
- A) Quieres máxima flexibilidad
- B) Quieres detectar cambios de schema inesperados y parar el pipeline
- C) No tienes schema definido
- D) Usas Parquet

**P5.** Auto Loader garantiza que cada archivo se procesa:
- A) Al menos una vez
- B) Exactamente una vez
- C) Como máximo una vez
- D) Depende del formato

**P6.** File notification mode frente a directory listing:
- A) Es más simple y no requiere permisos extra
- B) Usa colas/eventos del cloud para escalar a millones de archivos
- C) Solo funciona en AWS
- D) Es el modo por defecto

**P7.** Auto Loader puede funcionar como fuente batch incremental programada usando:
- A) `.trigger(availableNow=True)` en el writeStream
- B) `spark.read` normal
- C) `COPY INTO` exclusivamente
- D) Un bucle while

---

## Soluciones
1. **B** — cloudFiles = Auto Loader. Es un formato de readStream.
2. **B** — Ahí guarda el schema inferido y el estado; sin él debe reinferir.
3. **C** — rescue = nada se pierde, nada rompe el schema.
4. **B** — Detección temprana de drift de schema (falla el batch, no el pipeline entero).
5. **B** — Exactly-once gracias al log de archivos procesados (RocksDB).
6. **B** — Notification mode (SQS/Event Grid/SNS) escala mejor que listar directorios.
7. **A** — Auto Loader + availableNow = patrón de ingesta incremental programada (el favorito del examen).
