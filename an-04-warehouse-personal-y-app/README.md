# AN-04 — Warehouse personal en estrella + app interactiva

> Proyectos 2 y 7 del *7 Data Analyst Projects Roadmap* unidos: un data warehouse personal con esquema en estrella y una app desplegada encima. En Databricks: Delta + Lakeflow Jobs + AI/BI/Genie + Databricks Apps (Streamlit).

## Objetivo

1. Construir un warehouse personal con **esquema en estrella** que combine una fuente pública (clima histórico de tu ciudad) con fuentes personales opcionales (exports de Spotify, banco... que NUNCA se suben a este repo).
2. Desplegar una **app interactiva** sobre esos datos que otra persona pueda usar sin tocar notebooks.

## Por qué importa

Un analista que solo hace notebooks entrega trabajo a medias: el producto final es algo que el negocio puede tocar. El esquema en estrella (hechos + dimensiones) es el diseño canónico que encontrarás en cualquier empresa, y montarlo sobre tus propios datos hace que lo entiendas de verdad — aquí los datos te importan, y eso se nota. La app cierra el círculo: de datos crudos a herramienta usable.

## Datos (URLs verificadas)

- Open-Meteo Archive API (clima diario histórico desde 1940, sin key): https://archive-api.open-meteo.com/v1/archive ✅ (params: `latitude, longitude, start_date, end_date, daily`)
- Datos abiertos España: https://datos.gob.es/ ✅
- Documentación Streamlit: https://docs.streamlit.io ✅
- Streamlit en Hugging Face Spaces (alternativa de despliegue): https://huggingface.co/docs/hub/spaces ✅
- Databricks Apps: https://docs.databricks.com/ → *Databricks Apps* ✅

## Prerrequisitos

- `an-01` (ya sabes capturar APIs y programar jobs), `dbx-03`, `dbx-10`.

## Tareas

### Parte A — Esquema en estrella

- [ ] Descarga el clima diario de tu ciudad desde Open-Meteo (varios años): `temperature_2m_max, temperature_2m_min, precipitation_sum, ...` → Bronze `personal.clima_raw`.
- [ ] Diseña en papel (o en `docs/modelo.md`) el esquema antes de codificar: `dim_fecha` (día, mes, trimestre, año, estación), `dim_ciudad`, `fact_clima_diario`. Claves surrogate o naturales: justifica tu elección.
- [ ] Construye las tres tablas en Delta con las claves correctas y **documenta cada columna** (`COMMENT ON COLUMN`).
- [ ] (Opcional, privacy-first) Añade una fuente personal: export de Spotify (solicítalo en tu cuenta) o CSV del banco. Va a un **UC Volume fuera del repo** y JAMÁS a Git. Si la usas: `fact_escuchas` / `fact_gastos` con sus dims.
- [ ] Job diario (Lakeflow Jobs) que actualice el clima incrementalmente (solo fechas nuevas: `MERGE` por fecha+ciudad).
- [ ] Prueba de calidad: expectativas/asserts — sin fechas duplicadas, sin nulos en claves, rango de temperaturas razonable (-30 a 50 °C).

### Parte B — Capa semántica y consumo

- [ ] 3–5 métricas de negocio sobre el esquema: días de lluvia por mes vs media histórica, anomalías de temperatura (día vs media de ese día del año), rachas de calor.
- [ ] Dashboard AI/BI + Genie configurado con instrucciones ("las temperaturas están en °C; 'racha de calor' = 3+ días >32 °C").
- [ ] Pregunta a Genie 5 cosas y anota en qué falla: ajusta sinónimos/instrucciones hasta que responda bien. Documenta el antes/después.

### Parte C — La app

- [ ] App Streamlit (`app.py`) que consulte las tablas con `databricks-sql-connector` o `databricks.sdk`: selector de ciudad y rango de fechas, gráfico de temperatura con anomalías marcadas, tabla de récords.
- [ ] Despliega en **Databricks Apps** si tu workspace lo permite; si no, en Hugging Face Spaces (gratis) leyendo de un export Parquet — el código debe parametrizar la fuente para ambas opciones.
- [ ] README de la app con captura y enlace público (si Spaces) en `docs/app.md`.
- [ ] Reglas de una buena app (del roadmap): un usuario que no te conoce debe entender qué ve en 10 segundos — título claro, un KPI arriba, un gráfico principal, filtros a la izquierda.

## Criterios de éxito

- [ ] Esquema en estrella real (no una tabla ancha): hechos y dimensiones con claves documentadas.
- [ ] Job diario incremental idempotente (reejecutar el mismo día no duplica).
- [ ] App desplegada y usable por un tercero sin explicaciones orales.
- [ ] Ningún dato personal en Git (compruébalo con `git log --stat`).

## Recursos

- Fuentes de datos: [../docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)
- Databricks SQL connector: https://docs.databricks.com/ → *Databricks SQL Connector for Python* ✅

## ⚠️ Seguridad y privacidad

- **Tus exports personales (Spotify, banco, Google) NUNCA entran en este repo ni en tablas compartidas**: UC Volume personal + `.gitignore`. Un CSV bancario commiteado es un incidente: si pasa, rótalo todo y reescribe el historial.
- El token de Databricks para la app va en Secrets/variables de entorno del despliegue, no en `app.py`.
- Open-Meteo no pide key: atribución agradecida en el README de la app.

## Entregable

`docs/modelo.md` + tablas del esquema + job incremental + `app.py` desplegada + `docs/app.md` con enlace/captura.
