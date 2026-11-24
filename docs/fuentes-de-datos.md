# Fuentes de datos públicas (URLs verificadas)

URLs comprobadas una a una antes de incluirlas. Úsalas en los ejercicios que las referencian.

| Fuente | URL | Uso en ejercicios | Estado |
|---|---|---|---|
| JSONPlaceholder | https://jsonplaceholder.typicode.com/ | wr-03 (feeds), wr-04 (APIs), ai-15 (tools externas) | ✅ verificada |
| FRED API (Federal Reserve) | https://fred.stlouisfed.org/docs/api/fred/ | wr-04 (API con key) | ✅ verificada |
| Citi Bike System Data | https://citibikenyc.com/system-data | wr-01, wr-05, wr-07 | ✅ verificada |
| NYC TLC Trip Records | https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page | wr-07, wr-08 (taxi) | ✅ verificada |
| datos.gob.es (España) | https://datos.gob.es/ | wr-03, wr-04 (datos abiertos ES) | ✅ verificada |
| Databricks Docs (oficial) | https://docs.databricks.com/ | todos los módulos ai-XX, dbx-XX | ✅ verificada |
| Databricks samples (catálogo `samples`) | — (integrado en el workspace) | wr-07, wr-08, ai-08, ai-15 | ✅ integrado |
| Databricks datasets (`/databricks-datasets`) | — (integrado en DBFS) | varios | ✅ integrado |
| Open-Meteo Archive API | https://archive-api.open-meteo.com/v1/archive | an-01, an-04 (clima histórico, sin key) | ✅ verificada |
| Books to Scrape | https://books.toscrape.com | an-01 (scraping de práctica legal) | ✅ verificada |
| google-play-scraper | https://github.com/JoMingyu/google-play-scraper | an-03 (reviews Google Play) | ✅ verificada |
| Arbeitnow Job API | https://www.arbeitnow.com/api/job-board-api | an-03 (ofertas empleo, sin key) | ✅ verificada |
| Remotive API | https://remotive.com/api/remote-jobs | an-03 (empleo remoto, sin key) | ✅ verificada |
| Evan Miller A/B tools | https://www.evanmiller.org/ab-testing | an-02 (significancia y sample size) | ✅ verificada |
| statsmodels | https://www.statsmodels.org/stable/index.html | an-01, an-02 (OLS, z-test) | ✅ verificada |
| NASA Anomaly Detection (Kaggle) | https://www.kaggle.com/datasets/patrickfleith/nasa-anomaly-detection-dataset-smap-msl | ml-01 (SMAP/MSL etiquetado) | ✅ verificada |
| NASA Exoplanet Archive | https://exoplanetarchive.ipac.caltech.edu/ | ml-01 (Kepler DR24 TCE) | ✅ verificada |
| lightkurve docs | https://lightkurve.github.io/lightkurve/ | ml-01 (curvas de luz) | ✅ verificada |
| AI4MARS (Hugging Face) | https://huggingface.co/datasets/hassanjbara/AI4MARS | ml-02 (segmentación Marte) | ✅ verificada |
| Gradio docs | https://www.gradio.app/docs | ml-02 (demo) | ✅ verificada |
| Streamlit docs | https://docs.streamlit.io | an-04 (app) | ✅ verificada |
| Hugging Face Spaces | https://huggingface.co/docs/hub/spaces | an-04, ml-02 (despliegue gratis) | ✅ verificada |
| Kaggle API docs | https://www.kaggle.com/docs/api | ml-01 (descarga con key en Secrets) | ✅ verificada |

## Notas

- **FRED**: requiere API key gratuita → https://fredaccount.stlouisfed.org/apikeys (guárdala en Databricks Secrets, nunca en código).
- **Citi Bike**: CSVs mensuales comprimidos; algunos meses vienen en varios ficheros.
- **NYC TLC**: Parquet mensual; desde 2025 incluye la columna `cbd_congestion_fee`.
- **Databricks samples**: `spark.table("samples.nyctaxi.trips")` y `%fs ls /databricks-datasets` — sin descarga ni URLs externas.
- **Open-Meteo**: sin API key; atribución agradecida. Params mínimos: `latitude, longitude, start_date, end_date, daily`.
- **Books to Scrape**: sitio creado expresamente para practicar scraping — es la opción legal segura antes de scrapear tiendas reales (revisa siempre robots.txt y ToS).
- **Kaggle**: requiere cuenta + API key (`~/.kaggle/kaggle.json`) → en Databricks, la key va en Secrets y el JSON se monta en un Volume, nunca en Git.
- **NASA (Exoplanet Archive, AI4MARS, SMAP/MSL)**: datos científicos públicos; cita la fuente en tus READMEs y demos.

## Track AI: política de datos

Los módulos `ai-XX` no dependen de datasets externos: sus golden sets, corpus de documentos, prompts de prueba y logs son **sintéticos y autocurados** (parte del aprendizaje es crearlos bien). Excepciones:

- `ai-08` usa `samples.nyctaxi` (integrado) + un esquema propio con datos sintéticos.
- `ai-15` puede usar APIs públicas de esta tabla como herramientas externas (JSONPlaceholder).
- `ai-10` puede usar texto fuente de cualquier dataset público de esta tabla para construir el dataset de fine-tuning.

Si alguna URL deja de funcionar, abre un issue en este repo.
