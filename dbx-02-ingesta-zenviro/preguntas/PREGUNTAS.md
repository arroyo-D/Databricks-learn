# Preguntas de práctica — dbx-02 / Dominio: Data Ingestion & Loading (21%)

**P1.** La sintaxis `SELECT * FROM json.\`abfss://.../locations\`` :
- A) Solo funciona con Delta
- B) Lee directamente archivos JSON sin crear tabla
- C) Requiere CREATE TABLE previo
- D) Solo funciona en notebooks Python

**P2.** Al hacer `DROP TABLE` sobre una tabla EXTERNA:
- A) Se borran los datos del storage
- B) Solo se elimina el registro del metastore; los ficheros permanecen
- C) Se borran datos y metadatos
- D) No se puede hacer DROP en tablas externas

**P3.** Una tabla MANAGED en Unity Catalog guarda sus datos en:
- A) El storage root del metastore / location del catálogo o schema
- B) El DBFS root del workspace siempre
- C) La external location indicada en el CREATE
- D) Memoria del cluster

**P4.** Para leer un archivo TSV la opción correcta es:
- A) `format("tsv")`
- B) `option("sep", "\\t")` con formato csv
- C) `option("delimiter", "tab")`
- D) No se puede, hay que convertir a CSV

**P5.** El formato `binaryFile` devuelve:
- A) Solo el contenido binario
- B) path, modificationTime, length y content
- C) Un DataFrame vacío
- D) El texto extraído del archivo

**P6.** Una vista TEMPORAL (CREATE TEMPORARY VIEW):
- A) Persiste entre sesiones
- B) Es visible para todos los usuarios
- C) Vive solo en la sesión actual del notebook
- D) Ocupa espacio en el metastore

**P7.** En Parquet, al crear una tabla externa NO declaras columnas porque:
- A) Parquet no tiene columnas
- B) El schema está embebido en el propio formato
- C) Databricks lo prohíbe
- D) Solo aplica a CSV

---

## Soluciones
1. **B** — `<format>.\`ruta\`` = lectura ad-hoc sin registrar tabla.
2. **B** — Clásica del examen: externa = DROP solo desregistra.
3. **A** — Managed = almacenamiento gestionado por UC (metastore root o location del catálogo/schema).
4. **B** — TSV se lee como CSV con `sep "\t"`.
5. **B** — Metadatos + content; ideal para imágenes/PDF.
6. **C** — TEMP VIEW = ámbito de sesión; GLOBAL TEMP VIEW = todos los notebooks del cluster.
7. **B** — Parquet es autodescriptivo (schema embebido).
