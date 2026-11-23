# Solución — WR-03

## 1. Delimitados
```python
df = (spark.read.option("header", True).option("inferSchema", True)
      .csv(f"{BASE}/ventas.csv"))

# CSV europeo
df_eu = (spark.read.option("header", True).option("sep", ";")
         .csv(f"{BASE}/ventas_eu.csv")
         .withColumn("importe", F.regexp_replace("importe", ",", ".").cast("double")))

# TSV / pipe
spark.read.option("sep", "\t").option("header", True).csv(f"{BASE}/datos.tsv")
spark.read.option("sep", "|").option("header", True).csv(f"{BASE}/datos.psv")
```

## 2. Excel
```python
%pip install openpyxl
import pandas as pd
pdf = pd.read_excel("/Volumes/main/raw/wr/informe.xlsx", sheet_name="Ventas", skiprows=2)
df = spark.createDataFrame(pdf.astype(str))  # cuidado con tipos
```
Excel como intercambio: formato binario propietario, sin control de versiones dif-friendly, metadatos ocultos, límites de filas. CSV/Parquet > XLSX para datos.

## 3. Ancho fijo
```python
# "ANA  034MADRID   0012050" -> nombre(1:6), edad(7:9), ciudad(10:19), importe(20:26)
df_fw = spark.read.text(f"{BASE}/fijo.txt")
fw = df_fw.select(
    F.trim(F.substring("value", 1, 6)).alias("nombre"),
    F.substring("value", 7, 3).cast("int").alias("edad"),
    F.trim(F.substring("value", 10, 10)).alias("ciudad"),
    F.substring("value", 20, 7).cast("double").alias("importe"))
```

## 4. PDF
```python
%pip install pypdf
from pypdf import PdfReader
reader = PdfReader(f"{BASE}/doc.pdf")
texto = "\n".join(p.extract_text() for p in reader.pages)
```
Extracción de tablas (tabula/camelot) cuando el PDF tiene tablas bien delimitadas; texto libre + regex para documentos narrativos.

## 5. Feeds
```python
import requests
r = requests.get("https://jsonplaceholder.typicode.com/users")  # URL verificada
r.raise_for_status()
df_api = spark.createDataFrame(r.json())
```
`spark.read.json(ruta)` para ficheros; `requests` + `createDataFrame` para respuestas HTTP en memoria.

## 6. CSV roto
```python
df_bad = (spark.read.option("header", True)
    .option("mode", "PERMISSIVE")
    .option("columnNameOfCorruptRecord", "_corrupt")
    .option("encoding", "latin-1")
    .option("quote", '"').option("escape", '"')
    .option("badRecordsPath", f"{BASE}/bad_records")
    .csv(f"{BASE}/roto.csv"))
```

## Puntos clave
- `PERMISSIVE` + `badRecordsPath` = nunca perder datos por un archivo sucio.
- `inferSchema` solo en exploración; en producción, schema explícito.
- Ancho fijo sigue vivo en banca/administración: `substring` es tu amigo.
