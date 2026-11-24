# ML-01 — Series temporales NASA: detección de anomalías + exoplanetas

> Proyectos 3 y 1 del *Space ML Project Guides* unidos: ambos son machine learning sobre **series temporales reales de la NASA** (telemetría de sondas y curvas de luz de estrellas), con datos públicos etiquetados y baselines obligatorios. Corre en un cluster normal de Databricks (CPU), sin GPU.

## Objetivo

1. **Anomalías en telemetría**: entrenar un LSTM sobre los canales etiquetados de las sondas SMAP/MSL y detectar anomalías con un umbral dinámico, comparando contra un baseline estadístico.
2. **Exoplanetas**: clasificar candidatos a tránsito planetario del catálogo Kepler DR24, con split por estrella y baseline de gradient boosting antes de la CNN.

## Por qué importa

Estos dos proyectos enseñan lo que los datasets de juguete no pueden: **evaluar por eventos y no por puntos** (una anomalía detectada tarde o a trozos no cuenta igual), **evitar el leakage por agrupación** (dos curvas de la misma estrella en train y test = resultados inflados que no sobreviven a producción) y **justificar cuándo el deep learning gana al baseline** (y cuándo no). Además, las etiquetas son reales: científicos de la NASA las validaron. MLflow registra cada experimento para que la comparación sea honesta.

## Datos (URLs verificadas)

- NASA Anomaly Detection (SMAP/MSL, 82 canales, 105 anomalías etiquetadas) — Kaggle: https://www.kaggle.com/datasets/patrickfleith/nasa-anomaly-detection-dataset-smap-msl ✅
- NASA Exoplanet Archive (tabla TCE Kepler DR24): https://exoplanetarchive.ipac.caltech.edu/ ✅ y su API: https://exoplanetarchive.ipac.caltech.edu/docs/program_interfaces.html ✅
- lightkurve (curvas de luz): https://lightkurve.github.io/lightkurve/ ✅
- Kaggle API (para descargar): https://www.kaggle.com/docs/api ✅

## Prerrequisitos

- `wr-05`, `wr-07` (pandas/EDA sólidos) y `dbx-03`. Recomendado: `ai-01` (cultura de evaluación).

## Tareas

### Parte A — Anomalías SMAP/MSL

- [ ] Descarga el dataset con la Kaggle API (key en **Databricks Secrets**) a un UC Volume. Lee el README del dataset: entrena SOLO en datos normales.
- [ ] EDA de 3 canales: plotea la serie, marca el intervalo anómalo etiquetado, mira el ruido del canal.
- [ ] **Baseline z-score**: anomalía = |z| > k sobre la señal (o sobre su residuo con media móvil). Evalúa contra las etiquetas.
- [ ] **LSTM predictor**: ventanas de L=250 pasos → predice el siguiente valor. 2 capas LSTM de 80, dropout 0,3. Error = |real − predicho|.
- [ ] **Suavizado EWMA** del error (span=30) y **umbral dinámico** μ + k·σ sobre el error suavizado, con k entre 2,5 y 10 (barre k y quédate con el mejor).
- [ ] **Scoring por eventos**: una anomalía detectada cuenta como acierto si toca el intervalo etiquetado (no por puntos individuales). Implementa precision/recall por evento con pruning de falsos positivos consecutivos.
- [ ] Todo en **MLflow**: params (L, dropout, k), métricas (P/R/F1 por evento) del baseline y del LSTM, y la figura de la detección sobre un canal como artifact.
- [ ] Referencia del paper original: ~87,5% precision / 80% recall con el mejor setup. ¿Te acercas? ¿Con qué k?
- [ ] Escribe 3 líneas: ¿cuándo recomendarías el z-score en vez del LSTM?

### Parte B — Exoplanetas Kepler

- [ ] Descarga la tabla **TCE DR24** del Exoplanet Archive (TAP query o CSV desde la web). OJO con las columnas.
- [ ] **Regla de oro anti-leakage**: no uses `av_pred_class` ni columnas `av_pp_*` ni nada que sea una predicción previa como feature — son la respuesta disfrazada. Lista qué columnas descartas y por qué.
- [ ] **Unidades**: `tce_duration` viene en **horas** y `tce_period` en **días**. Normaliza o te comerás un bug silencioso.
- [ ] **Baseline**: `HistGradientBoostingClassifier` (o LightGBM) sobre las features de tabla. Métrica: **AUC**, no accuracy (clases muy desbalanceadas).
- [ ] **Split por estrella**: `GroupShuffleSplit` con grupo = `kepid`. Verifica que ninguna estrella aparece en train y test a la vez (asérvalo en código).
- [ ] **CNN 1D** (opcional pero recomendado): con `lightkurve` descarga curvas de luz de una muestra de estrellas, aplica `flatten` después de **enmascarar los tránsitos conocidos** (si no, el flatten se come la señal), genera vista global + vista local y entrena una CNN 1D de dos entradas.
- [ ] Compara en MLflow: baseline vs CNN (AUC + curva PR). ¿Cuánto gana la CNN? ¿Merece la complejidad?
- [ ] README del módulo con tabla baseline-vs-modelo, una figura estrella (curva de luz con el tránsito marcado) y sección *Limitations*.

## Criterios de éxito

- [ ] Baseline antes que modelo complejo, en ambas partes, con métricas comparadas en MLflow.
- [ ] Scoring por eventos implementado y explicado (no accuracy por puntos).
- [ ] Split por estrella verificado con un assert; columnas de leakage documentadas.
- [ ] AUC como métrica principal y desbalanceo de clases tratado explícitamente.

## Recursos

- Documentación MLflow en Databricks: https://docs.databricks.com/ → *MLflow* ✅
- Fuentes de datos: [../docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)

## ⚠️ Free Edition

- **Descarga**: Kaggle y el Exoplanet Archive pueden no ser dominios de confianza para la salida restringida. Descarga en tu Mac/servidor con la Kaggle API (key en `~/.kaggle/kaggle.json` local, jamás en Git) y sube los ficheros a un UC Volume: [../docs/recursos-gratuitos.md](../docs/recursos-gratuitos.md) §3.
- **Entrenamiento**: el LSTM (Parte A) y el baseline (Parte B) entrenan en CPU. Caben en serverless, pero vigila la cuota diaria; si la agotas, entrena en tu servidor (28 núcleos) apuntando MLflow al workspace: [../docs/servidor-local.md](../docs/servidor-local.md) §5.

## ⚠️ Seguridad

- La Kaggle API key va en **Databricks Secrets** (`dbutils.secrets.get(scope, "kaggle-key")`), nunca en el notebook ni en `~/.kaggle/kaggle.json` commiteado.
- Datos científicos públicos: sin restricciones, pero cita la fuente (NASA Exoplanet Archive / dataset de Kaggle) en tu README.

## Entregable

Notebooks de ambas partes + experimentos MLflow + README del módulo con tabla comparativa y figura estrella.
