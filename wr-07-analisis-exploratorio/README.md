# WR-07 — Análisis Exploratorio de Datos (EDA)

> Capítulo 9 de McGregor con PySpark: tendencia central, outliers, distribuciones y proporciones.

## Objetivo
Hacer un EDA completo y honesto sobre un dataset real.


## Por qué importa

Antes de modelar o transformar hay que mirar: distribuciones, nulos, correlaciones, outliers. El EDA en Databricks usa Spark para los agregados (millones de filas) y pandas/plotting solo sobre muestras o resultados agregados — un patrón que debes interiorizar para no reventar el driver. Usarás los datos de taxi de NYC y Citi Bike, datasets reales y verificados.

## Datos
- NYC Taxi integrado en Databricks: `samples.nyctaxi.trips` (sin descarga) ✅
- NYC TLC Trip Records (Parquet mensual): https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page ✅
- Citi Bike: https://citibikenyc.com/system-data ✅

## Tareas

### 1. Contexto primero
- [ ] Antes de tocar datos: escribe en Markdown 3 preguntas de negocio que quieres responder y qué esperas encontrar

### 2. Tendencia central
- [ ] Media vs mediana de la variable principal (duración/importe): ¿cuándo difieren y por qué?
- [ ] Usa `mean`, `percentile_approx` y `mode`

### 3. Outliers
- [ ] Detecta outliers con la regla IQR (Q1 - 1.5·IQR, Q3 + 1.5·IQR)
- [ ] Detecta con z-score (> 3)
- [ ] Decide: ¿son errores o valores reales? Justifica en Markdown

### 4. Distribuciones
- [ ] Histograma de la variable principal (gráfico nativo de `display()`)
- [ ] ¿Es simétrica? ¿cuántos "clusters" (modas) ves?
- [ ] Boxplot por categoría

### 5. Proporciones
- [ ] % de viajes por tipo de usuario / por franja horaria
- [ ] Tabla cruzada (crosstab) de dos variables categóricas

### 6. La pregunta de los 2M$
- [ ] Formula UNA pregunta de negocio cuantificable y respóndela con datos, mostrando el cálculo paso a paso

## Criterios de éxito

- [ ] Perfil completo del dataset (nulos, cardinalidad, distribuciones) en una celda reutilizable.
- [ ] 3 hipótesis de negocio formuladas y comprobadas con datos.
- [ ] Agregación en Spark → muestra a pandas → visualización, sin `toPandas()` sobre la tabla entera.

## Recursos

- Fuentes de datos (taxi NYC, Citi Bike): [docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)

## Entregable
Notebook `notebooks/wr07_eda.py` con secciones Markdown explicando CADA hallazgo (no solo código).