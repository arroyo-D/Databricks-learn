# Solución — Ejercicio 7

## 1. DDL
```sql
CREATE TABLE IF NOT EXISTS zenviro.silver.products (
  product_id INT NOT NULL,
  product_name STRING,
  quantity INT CHECK (quantity >= 0),
  price DOUBLE,
  total DOUBLE GENERATED ALWAYS AS (quantity * price)
) USING DELTA;

CREATE OR REPLACE TABLE zenviro.silver.products (...);  -- recrea
INSERT INTO  zenviro.silver.products VALUES (...);      -- añade
INSERT OVERWRITE zenviro.silver.products VALUES (...);  -- reemplaza particiones/datos
```

## 2-3. Historial y time travel
```sql
DESCRIBE HISTORY zenviro.silver.products;

SELECT * FROM zenviro.silver.products VERSION AS OF 0;
SELECT * FROM zenviro.silver.products TIMESTAMP AS OF '2026-11-23 10:00:00';

RESTORE TABLE zenviro.silver.products TO VERSION AS OF 0;
```
El transaction log (`_delta_log/*.json`) registra cada commit: archivos añadidos/eliminados. ACID = cada escritura es un commit atómico.

## 4. VACUUM
```python
spark.conf.set("spark.databricks.delta.retentionDurationCheck.enabled", "false")
```
```sql
VACUUM zenviro.silver.products RETAIN 0 HOURS;
-- Ojo: las versiones anteriores dejan de ser accesibles
```

## 5. COPY INTO y MERGE
```sql
COPY INTO zenviro.silver.products
FROM '/Volumes/zenviro/raw/data/products'
FILEFORMAT = CSV
FORMAT_OPTIONS ('header' = 'true');
-- Relanzar no duplica: COPY INTO lleva registro de archivos cargados

MERGE INTO zenviro.silver.products t
USING updates_products s
ON t.product_id = s.product_id
WHEN MATCHED AND s.deleted = true THEN DELETE
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *;
```

## 6. Compactación
```sql
OPTIMIZE zenviro.silver.products;
OPTIMIZE zenviro.silver.products ZORDER BY (product_id);

CREATE TABLE zenviro.silver.products_lc (
  product_id INT, product_name STRING, price DOUBLE
) USING DELTA
CLUSTER BY (product_id);
```

## Puntos de examen
- Delta = Parquet + transaction log. Z-ORDER reordena datos dentro de archivos; Liquid Clustering es la alternativa moderna (no requiere OPTIMIZE ZORDER).
- `VACUUM` borra archivos físicos antiguos (retención por defecto 7 días).
- `MERGE` = upsert estándar; `COPY INTO` es idempotente.
- `INSERT OVERWRITE` reemplaza datos; `INSERT INTO` añade.
