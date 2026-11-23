# Databricks Learn

Programa completo de aprendizaje de Databricks, de cero a certificación, basado en:

1. **Curso**: Databricks Certified Data Engineer Associate (Udatademy) → track `dbx-XX`
2. **Libro**: *Practical Python Data Wrangling and Data Quality* (McGregor) → track `wr-XX`
3. **Libro**: *Designing Data Governance from the Ground Up* (Maffeo) → track `gov-XX`

## Estructura

Cada módulo es una carpeta con:

- `README.md` → enunciado: objetivos, tareas con checklist y entregable
- `correccion/SOLUCION.md` → solución con código y puntos clave (consúltala DESPUÉS de intentarlo)
- `preguntas/PREGUNTAS.md` → preguntas de práctica originales estilo examen (solo track dbx)

## Track 1 — Certificación (dbx)

| # | Módulo | Dominio del examen |
|---|---|---|
| 00 | [Setup y fundamentos](dbx-00-setup) | Intelligence Platform 6% |
| 01 | [Unity Catalog setup](dbx-01-unity-catalog-setup) | Governance 15% |
| 02 | [Ingesta Zenviro](dbx-02-ingesta-zenviro) | Ingestion 21% |
| 03 | [Transformaciones Bronze→Gold](dbx-03-transformaciones) | Transformation 22% |
| 04 | [PySpark](dbx-04-python-spark) | Transformation 22% |
| 05 | [Structured Streaming](dbx-05-structured-streaming) | Ingestion / Troubleshooting |
| 06 | [Auto Loader](dbx-06-auto-loader) | Ingestion 21% |
| 07 | [Delta Lake](dbx-07-delta-lake) | Transformation / Optimization 10% |
| 08 | [Lakeflow Jobs](dbx-08-lakeflow-jobs) | Lakeflow Jobs 16% |
| 09 | [Lakeflow SDP](dbx-09-lakeflow-sdp) | Lakeflow Jobs 16% |
| 10 | [Databricks SQL](dbx-10-databricks-sql) | Platform / Production |
| 11 | [Gobernanza UC](dbx-11-gobernanza) | Governance 15% |
| 12 | [Delta Sharing + Federation](dbx-12-delta-sharing-federation) | Governance 15% |
| 13 | [Databricks Connect](dbx-13-databricks-connect) | CI/CD 10% |
| 14 | [Asset Bundles](dbx-14-asset-bundles) | CI/CD 10% |

## Track 2 — Data Wrangling & Data Quality (wr)

| # | Módulo |
|---|---|
| 01 | [Python fundamentos](wr-01-python-fundamentos) |
| 02 | [Calidad de datos: conceptos](wr-02-calidad-datos-conceptos) |
| 03 | [Ingesta de formatos](wr-03-ingesta-formatos) |
| 04 | [APIs y web scraping](wr-04-apis-web-scraping) |
| 05 | [Limpieza y transformación](wr-05-limpieza-transformacion) |
| 06 | [Código limpio y refactor](wr-06-codigo-limpio-refactor) |
| 07 | [Análisis exploratorio](wr-07-analisis-exploratorio) |
| 08 | [Visualización](wr-08-visualizacion) |

## Track 3 — Data Governance (gov)

| # | Módulo |
|---|---|
| 01 | [Framework de gobernanza](gov-01-framework-gobernanza) |
| 02 | [Roles y data stewards](gov-02-roles-stewards) |
| 03 | [Consejo y diccionario de datos](gov-03-consejo-diccionario-datos) |
| 04 | [Roadmap y evaluación](gov-04-roadmap-evaluacion) |
| 05 | [Desarrollo gobernado](gov-05-desarrollo-gobernado) |
| 06 | [Monitorización en producción](gov-06-monitorizacion-produccion) |

## Fuentes de datos

Los ejercicios usan datasets públicos verificados: ver [docs/fuentes-de-datos.md](docs/fuentes-de-datos.md).

## ⚠️ Seguridad (repo público)

- **Nunca** commitees tokens, API keys, contraseñas ni archivos `.databrickscfg`.
- Las credenciales van en Databricks Secrets (`dbutils.secrets`) o en variables de entorno locales.
- Todos los tokens/keys que aparecen en los ejemplos son **placeholders** (`dapiXXXX...`, `TU_CLAVE`).
- Revisa `git diff --cached` antes de cada push y mantén el `.gitignore` actualizado.
