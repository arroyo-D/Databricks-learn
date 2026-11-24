# SOLUCIÓN — AN-01 (léela DESPUÉS de intentarlo)

## Parte A — Tracker de precios

### Esqueleto del notebook de scraping

```python
import requests, datetime
from bs4 import BeautifulSoup
from pyspark.sql import Row

URLS = {
    "libro_001": "https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html",
    # ... tus 10-20 productos
}

hoy = datetime.date.today().isoformat()
filas = []
for pid, url in URLS.items():
    r = requests.get(url, timeout=15)
    r.raise_for_status()
    soup = BeautifulSoup(r.text, "html.parser")
    filas.append(Row(
        producto_id=pid,
        nombre=soup.select_one("h1").text.strip(),
        precio_raw=soup.select_one("p.price_color").text,   # se limpia en Silver
        disponibilidad=soup.select_one("p.availability").text.strip(),
        fecha_captura=hoy,
    ))

df = spark.createDataFrame(filas)
df.write.format("delta").mode("append").saveAsTable("tracker.precios_raw")
```

**Errores comunes:**
- Limpiar el precio en Bronze. NO: Bronze guarda `precio_raw` tal cual (`Â£51.77`); la limpieza (quitar símbolos raros, `float`) va en Silver. Si la limpieza tiene un bug, Bronze sigue intacto y reejecutas.
- Usar `.mode("overwrite")`: destruye el histórico. Siempre `append` con `fecha_captura` como clave de partición lógica.
- Scrapear en el momento del análisis: un tracker sin job programado no es un tracker, es una foto.

### Job diario

En *Workflows → Create Job*: una tarea tipo notebook, schedule cron `15 7 * * *` (07:15 diario), cluster serverless o el más pequeño. Comprueba en *Job runs* que aparece una ejecución diaria verde.

### Silver

```sql
CREATE OR REPLACE TABLE tracker.precios AS
SELECT DISTINCT
  producto_id,
  nombre,
  CAST(regexp_replace(precio_raw, '[^0-9.]', '') AS DOUBLE) AS precio,
  fecha_captura
FROM tracker.precios_raw;
```

## Parte B — Pregunta afilada

Ejemplo de flujo (la pregunta es tuya, el patrón es este):

1. En https://datos.gob.es/ busca 2 datasets oficiales (INE, MITMA, Banco de España...). Descarga CSV → UC Volume → `CREATE TABLE ... USING csv OPTIONS (header true)` o Auto Loader.
2. Normaliza claves de join (nombre de provincia con tildes/mayúsculas distintas es el enemigo nº 1: crea una columna `provincia_norm` con `upper(trim(...))` y sin tildes).
3. Regresión con statsmodels:

```python
import statsmodels.api as sm
pdf = spark.table("analisis.vivienda_salario").toPandas()
X = sm.add_constant(pdf[["anyo", "salario_medio"]])
modelo = sm.OLS(pdf["precio_m2"], X).fit()
print(modelo.summary())
```

**Interpretación mínima:** coeficiente = cuánto cambia Y por unidad de X; p-valor < 0,05 = probablemente no es azar; R² = qué fracción de la varianza explica el modelo. Si R² es 0,3, tu `conclusiones.md` debe decir que el 70% queda sin explicar.

**Errores comunes:**
- Correlación ≠ causalidad: dilo explícitamente en *Limitaciones*.
- Juntar fuentes con granularidades distintas (mensual vs anual) sin agregar primero.
- Concluir con un adjetivo ("sube mucho") en vez de un número ("+6,2 % anual vs +2,1 % del salario").

## Parte C — Dashboard

En AI/BI: dataset sobre `tracker.precios` y tu tabla de análisis. La gráfica estrella del tracker es un `line chart` de precio por día con una línea por producto; el que más baja se calcula con una ventana `first_value/last_value` o con `min/max` por producto.

## Qué decir en una entrevista

> "Monté un pipeline diario con Lakeflow Jobs que scrapea precios durante 30 días en Delta con capas Bronze/Silver, y un estudio cruzando dos fuentes del INE donde demostré con una regresión que X. Lo más difícil fue normalizar las claves de provincia entre fuentes."
