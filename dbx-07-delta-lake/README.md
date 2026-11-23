# Ejercicio 7 — Delta Lake a fondo

## Objetivo
Dominar Delta Lake: transacciones ACID, transaction log, time travel, VACUUM, MERGE, COPY INTO y compactación.


## Por qué importa

Delta Lake es el formato que hace posible el lakehouse: transacciones ACID sobre object storage, time travel y optimizaciones como OPTIMIZE/ZORDER. Cae en transformación (**22%**) y optimización (**10%**). Todo lo que has escrito en módulos anteriores era Delta — ahora aprendes por qué funciona y cómo mantenerlo sano (VACUUM incluido).

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

## Criterios de éxito

- [ ] Time travel usado para recuperar una tabla tras un DELETE erróneo.
- [ ] OPTIMIZE + ZORDER aplicados y explicado cuándo NO usar ZORDER.
- [ ] Historial (`DESCRIBE HISTORY`) interpretado: versiones, operaciones, métricas.

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Data engineering* → Delta Lake)

## Entregable
Notebook `notebooks/07_delta_lake.sql` + nota `docs/delta_vs_parquet.md`.