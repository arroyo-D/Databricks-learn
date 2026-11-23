# Ejercicio 7 — Delta Lake a fondo

## Objetivo
Dominar Delta Lake: transacciones ACID, transaction log, time travel, VACUUM, MERGE, COPY INTO y compactación.

## Tareas

### 1. CREATE TABLE en Delta
- [ ] `CREATE TABLE` con columnas y `USING DELTA`
- [ ] `CREATE OR REPLACE`, `INSERT OVERWRITE` vs `INSERT INTO`
- [ ] Columnas generadas y constraints (`NOT NULL`, `CHECK`)

### 2. Transaction log y ACID
- [ ] Hacer varias escrituras y revisar `_delta_log/` con `%fs ls`
- [ ] `DESCRIBE HISTORY` de la tabla
- [ ] Explicar en qué consiste la atomicidad con el transaction log

### 3. Time travel
- [ ] `SELECT * FROM tabla VERSION AS OF 0` y `TIMESTAMP AS OF`
- [ ] `RESTORE TABLE ... TO VERSION AS OF`

### 4. VACUUM
- [ ] Ejecutar `VACUUM tabla RETAIN 0 HOURS` (con spark.conf si hace falta) y comprobar que el time travel anterior ya no funciona

### 5. COPY INTO y MERGE
- [ ] Cargar archivos con `COPY INTO` (idempotente: relanzar no duplica)
- [ ] `MERGE INTO` con `WHEN MATCHED UPDATE`, `WHEN NOT MATCHED INSERT`, `WHEN MATCHED ... DELETE`

### 6. Compactación
- [ ] Generar muchos archivos pequeños y ejecutar `OPTIMIZE`
- [ ] `OPTIMIZE ... ZORDER BY (col)`
- [ ] Crear una tabla con **Liquid Clustering**: `CLUSTER BY (col)` y comparar

## Entregable
Notebook `notebooks/07_delta_lake.sql` + nota `docs/delta_vs_parquet.md`.
