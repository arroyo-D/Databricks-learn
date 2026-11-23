# Fuentes de datos públicas (URLs verificadas)

URLs comprobadas una a una antes de incluirlas. Úsalas en los ejercicios que las referencian.

| Fuente | URL | Uso en ejercicios | Estado |
|---|---|---|---|
| JSONPlaceholder | https://jsonplaceholder.typicode.com/ | wr-03 (feeds), wr-04 (APIs) | ✅ verificada |
| FRED API (Federal Reserve) | https://fred.stlouisfed.org/docs/api/fred/ | wr-04 (API con key) | ✅ verificada |
| Citi Bike System Data | https://citibikenyc.com/system-data | wr-01, wr-05, wr-07 | ✅ verificada |
| NYC TLC Trip Records | https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page | wr-07, wr-08 (taxi) | ✅ verificada |
| datos.gob.es (España) | https://datos.gob.es/ | wr-03, wr-04 (datos abiertos ES) | ✅ verificada |
| Databricks samples (catálogo `samples`) | — (integrado en el workspace) | wr-07, wr-08 | ✅ integrado |
| Databricks datasets (`/databricks-datasets`) | — (integrado en DBFS) | varios | ✅ integrado |

## Notas

- **FRED**: requiere API key gratuita → https://fredaccount.stlouisfed.org/apikeys (guárdala en Databricks Secrets, nunca en código).
- **Citi Bike**: CSVs mensuales comprimidos; algunos meses vienen en varios ficheros.
- **NYC TLC**: Parquet mensual; desde 2025 incluye la columna `cbd_congestion_fee`.
- **Databricks samples**: `spark.table("samples.nyctaxi.trips")` y `%fs ls /databricks-datasets` — sin descarga ni URLs externas.

Si alguna URL deja de funcionar, abre un issue en este repo.
