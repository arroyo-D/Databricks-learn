# Solución — Ejercicio 2

Ruta base usada (ajusta a tu external location / volumen):
`/Volumes/zenviro/raw/data/`

## locations — JSON simple
```sql
SELECT * FROM json.`/Volumes/zenviro/raw/data/locations`;

CREATE OR REPLACE VIEW zenviro.raw.v_locations AS
SELECT * FROM json.`/Volumes/zenviro/raw/data/locations`;

CREATE OR REPLACE TEMPORARY VIEW tv_locations AS
SELECT * FROM json.`/Volumes/zenviro/raw/data/locations`;
```

## departments — JSON simple (Tarea)
```sql
SELECT * FROM json.`/Volumes/zenviro/raw/data/departments`;
```

## job_positions / employees — JSON complejo como TEXT
```sql
CREATE OR REPLACE TEMPORARY VIEW tv_job_positions AS
SELECT * FROM text.`/Volumes/zenviro/raw/data/job_positions`;

CREATE OR REPLACE TEMPORARY VIEW tv_employees AS
SELECT * FROM text.`/Volumes/zenviro/raw/data/employees`;
```
Leer como TEXT conserva cada línea como string; el parseo a JSON se hace en Bronze.

## photochecks / pictures — binario
```sql
SELECT path, length, modificationTime
FROM binaryFile.`/Volumes/zenviro/raw/data/photochecks`;
```

## attendances / employment_histories — TSV
```sql
SELECT * FROM read_files(
  '/Volumes/zenviro/raw/data/attendances',
  format => 'csv', sep => '\t', header => true
);
```
(o `csv.` con opciones si tu runtime no soporta `read_files` en esta sintaxis)

## trainings — CSV como tabla externa
```sql
CREATE TABLE IF NOT EXISTS zenviro.raw.trainings (
  training_id INT, title STRING, category STRING, duration_hours INT, cost DOUBLE
)
USING CSV
OPTIONS (header 'true')
LOCATION '/Volumes/zenviro/raw/data/trainings';
```

## benefits — Parquet como tabla externa
```sql
CREATE TABLE IF NOT EXISTS zenviro.raw.benefits
USING PARQUET
LOCATION '/Volumes/zenviro/raw/data/benefits';
```
En Parquet el schema viene embebido: no hace falta declarar columnas.

## employee_trainings / employee_benefits (Tareas)
Igual que arriba, cambiando nombre y LOCATION.

## Puntos de examen
- `SELECT * FROM <formato>.\`ruta\`` = lectura directa sin tabla.
- Externa vs managed: con externa, `DROP TABLE` solo quita el registro del metastore.
- `binaryFile` devuelve metadatos + content; sirve para fotos/PDF.
