# Ejercicio 2 — Ingesta del Proyecto Zenviro (capa RAW)

## Objetivo
Leer todos los formatos del proyecto Zenviro (RRHH) desde el Data Lake hacia el catálogo: JSON simple, JSON complejo, CSV, TSV, Parquet y binarios.

## Dataset Zenviro
| Entidad | Formato |
|---|---|
| locations | JSON simple |
| departments | JSON simple |
| job_positions | JSON complejo (anidado) |
| employees | JSON complejo (string JSON) |
| pictures, photochecks | Binario (imágenes) |
| attendances, employment_histories | TSV |
| trainings, employee_trainings | CSV |
| benefits, employee_benefits | Parquet |

> **Datos**: los ficheros vienen con el curso. Si quieres practicar con formatos reales adicionales, usa las [fuentes públicas verificadas](../docs/fuentes-de-datos.md).

## Tareas

### 1. Cargar datos al Data Lake
- [ ] Subir los ficheros del curso a la external location (o volumen `zenviro.raw.data`)
- [ ] Listar archivos y carpetas con `%fs ls` / `dbutils.fs.ls`

### 2. Lecturas en SQL
- [ ] Leer `locations` con `SELECT * FROM json.\`<ruta>\``
- [ ] Crear vista y vista temporal de `locations`
- [ ] Leer `job_positions` (JSON complejo) como TEXT
- [ ] Leer `photochecks` con `binaryFile`
- [ ] Leer `attendances` (TSV) con opciones `sep`, `header`
- [ ] Crear tablas externas: `trainings` (CSV) y `benefits` (PARQUET)
- [ ] Registrar todo en `zenviro.raw`

### 3. Tareas del curso (hazlas tú antes de mirar la corrección)
- [ ] Leer `departments` (JSON simple)
- [ ] Leer `employees` (JSON complejo a TEXT)
- [ ] Leer `pictures` (binario)
- [ ] Leer `employment_histories` (TSV)
- [ ] Leer `employee_trainings` (CSV) y `employee_benefits` (Parquet)

## Conceptos clave
- Tabla **externa**: datos en tu storage; si haces DROP no se borran los ficheros.
- Tabla **managed**: Databricks gestiona los datos; DROP borra los datos.

## Entregable
Notebooks en `notebooks/` (uno por formato o uno único con secciones) ejecutados sin errores.
