# WR-08 — Visualización de Datos

> Capítulo 10 de McGregor: elegir el gráfico correcto, escala, color y anotación — con matplotlib/seaborn y los gráficos nativos de Databricks.

## Objetivo
Comunicar datos con visualizaciones correctas y honestas.

## Datos
- NYC Taxi integrado: `samples.nyctaxi.trips` ✅
- Cualquier tabla gold de los ejercicios dbx

## Tareas

### 1. El gráfico correcto para cada mensaje
- [ ] Pie (≤5 categorías) vs barras: haz el mismo dato en ambos y justifica cuál gana
- [ ] Línea: serie temporal de viajes por día
- [ ] Scatter: relación distancia vs importe
- [ ] Mapa: usa la visualización de mapa nativa de Databricks con lat/long

### 2. Gráficos nativos de Databricks
- [ ] Crea 3 visualizaciones desde `display(df)` con el editor de plots
- [ ] Exporta una a un dashboard (o al ejercicio dbx-10)

### 3. matplotlib + seaborn
- [ ] Histograma con matplotlib sobre una muestra (`df.sample()` + `toPandas()`)
- [ ] Boxplot por categoría con seaborn
- [ ] Heatmap de correlaciones
- [ ] Muestra la figura con `display(fig)`

### 4. Detalles que importan
- [ ] Mismo gráfico, dos versiones: eje Y truncado vs eje desde cero → escribe qué cambia en la percepción
- [ ] Paleta de colores apta para daltónicos (`colorblind` de seaborn)
- [ ] Anota el punto clave del gráfico (media, outlier, hito) con `ax.annotate`

### 5. Honestidad visual
- [ ] Encuentra (o fabrica) un gráfico engañoso y explica el truco en `docs/honestidad_visual.md`

## Entregable
Notebook `notebooks/wr08_viz.py` + `docs/honestidad_visual.md`.
