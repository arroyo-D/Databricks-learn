# Solución — Ejercicio 9

## Streaming table (SQL)
```sql
CREATE OR REFRESH STREAMING TABLE users
AS SELECT * FROM STREAM read_files(
  '/Volumes/rideshare/raw/users',
  format => 'json'
);
```

## Expectations (SQL)
```sql
CREATE OR REFRESH STREAMING TABLE users_valid (
  CONSTRAINT valid_email EXPECT (email IS NOT NULL) ON VIOLATION DROP ROW,
  CONSTRAINT valid_age   EXPECT (age > 0)
)
AS SELECT * FROM STREAM(LIVE.users_raw);
```

## Python — pyspark.pipelines
```python
from pyspark import pipelines as dp
from pyspark.sql import functions as F

@dp.table(name="payments")
@dp.expect_or_drop("valid_amount", "amount > 0")
def payments():
    return (spark.readStream.format("cloudFiles")
            .option("cloudFiles.format", "json")
            .load("/Volumes/rideshare/raw/payments"))
```

## SCD2 — Auto CDC
```python
dp.create_streaming_table("payments_scd2")

dp.create_auto_cdc_flow(
    target="payments_scd2",
    source="payments",
    keys=["payment_id"],
    sequence_by=F.col("updated_at"),
    stored_as_scd_type=2,
)
```

## Materialized views (Gold)
```sql
CREATE OR REFRESH MATERIALIZED VIEW driver_revenue_summary AS
SELECT d.driver_id, d.name, SUM(t.fare) AS total_revenue, COUNT(*) AS trips
FROM LIVE.trips t
JOIN LIVE.drivers d ON t.driver_id = d.driver_id
GROUP BY d.driver_id, d.name;

CREATE OR REFRESH MATERIALIZED VIEW payment_method_summary AS
SELECT method, COUNT(*) AS num_payments, SUM(amount) AS total
FROM LIVE.payments
GROUP BY method;
```

## Puntos de examen
- Streaming table = ingesta incremental; MV = transformación batch refrescada por el pipeline.
- Expectations: `expect` (warn), `expect_or_drop`, `expect_or_fail`.
- Auto CDC con `stored_as_scd_type = 2` genera `__START_AT` / `__END_AT`.
- Full refresh recalcula todo; incremental solo procesa datos nuevos en streaming tables.
