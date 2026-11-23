# Solución — GOV-05

## 1. Convenciones (ejemplo)
- Tablas: `<entidad>` en singular por capa (`employees`, `employee_benefits`), snake_case.
- Columnas: snake_case; fechas `_date`, timestamps `_ts`, flags `is_`/`has_`, ids `<entidad>_id`.
- Prohibido: espacios, mayúsculas, acentos, nombres genéricos (`data`, `tabla1`).

Validación:
```sql
SELECT table_schema, table_name
FROM system.information_schema.tables
WHERE table_catalog = 'zenviro'
  AND table_name !~ '^[a-z][a-z0-9_]*$';   -- no cumplen snake_case
```

## 2. Entornos
```sql
CREATE CATALOG IF NOT EXISTS zenviro_dev;
CREATE SCHEMA zenviro_dev.silver;
-- CI/CD (dbx-14) despliega de dev a prod; los desarrolladores no escriben en prod.
```

## 4. Flujo gobernado — nueva tabla `employee_absences`

Paso 1 — Diccionario primero (fila en data_dictionary / doc aprobado).

Paso 2 — DDL en dev con gobernanza incluida:
```sql
CREATE TABLE zenviro_dev.silver.employee_absences (
  absence_id BIGINT NOT NULL COMMENT 'Identificador único de ausencia',
  employee_id BIGINT COMMENT 'FK a silver.employees',
  absence_type STRING COMMENT 'vacaciones|baja|permiso',
  start_date DATE,
  end_date DATE,
  ingestion_ts TIMESTAMP
)
USING DELTA
COMMENT 'Ausencias de empleados. Fuente: HRIS. Diario.'
TBLPROPERTIES ('quality'='silver');

ALTER TABLE zenviro_dev.silver.employee_absences SET TAGS ('domain'='hr','sensitivity'='medium');
ALTER SCHEMA zenviro_dev.silver OWNER TO `hr_stewards`;
```

Paso 3 — Expectations:
```sql
CONSTRAINT pk_ok EXPECT (absence_id IS NOT NULL) ON VIOLATION FAIL
CONSTRAINT fechas_ok EXPECT (end_date >= start_date)
```

Paso 4 — PR en GitHub con el DDL + comentarios de revisión.
Paso 5 — Deploy a prod (bundle o SQL controlado) y auditoría posterior.

## 5. Sandbox
```sql
CREATE SCHEMA IF NOT EXISTS zenviro_dev.sandbox;
GRANT USE SCHEMA, CREATE TABLE, SELECT, MODIFY ON SCHEMA zenviro_dev.sandbox TO `data_analysts`;
```
Sandbox = espacio seguro para fallar; nada sale de ahí sin pasar por el flujo gobernado.

## Puntos clave
- Gobernanza en el diseño cuesta 1; parchearla después cuesta 10.
- La definición (diccionario) va ANTES del código.
- Sandbox + dev/prod = experimentación sin riesgo para producción.
