# WR-03 — Ingesta de Formatos de Archivo

> Capítulo 4 de McGregor, trasladado a Databricks: delimitados, Excel, ancho fijo, feeds y PDFs.

## Objetivo
Saber ingerir cualquier formato de fichero que te lancen, incluidos los "difíciles".

## Datos
- JSONPlaceholder (feed JSON de prueba): https://jsonplaceholder.typicode.com/ ✅
- Datos abiertos España (CSV/XLSX reales): https://datos.gob.es/ ✅
- Más fuentes verificadas: [docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)

## Tareas

### 1. Delimitados
- [ ] CSV estándar con `header` e `inferSchema`
- [ ] CSV europeo: separador `;`, decimal `,` → ¿cómo lo resuelves?
- [ ] TSV y pipe-delimited (`|`)

### 2. Excel
- [ ] Leer un `.xlsx` (pandas + openpyxl en el notebook) eligiendo hoja y saltando filas de cabecera
- [ ] ¿Por qué NO es buena idea usar Excel como formato de intercambio? Escríbelo en `docs/`.

### 3. Ancho fijo (fixed-width)
- [ ] Genera un fichero de ancho fijo y léelo cortando por posiciones con `substring`

### 4. PDF
- [ ] Extrae texto de un PDF con `pypdf` (en Databricks: `%pip install pypdf`)
- [ ] Comenta cuándo usarías extracción de tablas vs texto libre

### 5. Feeds (JSON/XML)
- [ ] Descarga un feed JSON público con `requests` y conviértelo en DataFrame
- [ ] Compara con leer el mismo JSON desde el volumen con `spark.read.json`

### 6. Reto
- [ ] Un CSV "roto": comillas sin cerrar, filas con columnas de más, encoding latin-1. Ingiérelo usando opciones (`mode`, `encoding`, `quote`, `escape`, `badRecordsPath`)

## Entregable
Notebook `notebooks/wr03_formatos.py` con una sección por formato + `docs/formatos.md` (tabla: formato → opciones clave).
