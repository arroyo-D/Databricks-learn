# Solución — Ejercicio 3

## Bronze — ejemplo locations
```sql
CREATE OR REPLACE TABLE zenviro.bronze.locations AS
SELECT
  CAST(location_id AS INT) AS location_id,
  INITCAP(city) AS city,
  UPPER(country) AS country,
  current_timestamp() AS ingestion_date
FROM zenviro.raw.v_locations;
```
Mismo patrón para el resto: cast de tipos, trim/upper/lower, `current_timestamp()` como `ingestion_date`.

## Silver — employees: string → JSON
```sql
CREATE OR REPLACE TABLE zenviro.silver.employees AS
SELECT
  from_json(value, 'employee_id INT, name STRING, department_id INT, skills ARRAY<STRING>').*
FROM zenviro.raw.tv_employees;
```

## Expandir arrays
```sql
SELECT employee_id, explode(skills) AS skill
FROM zenviro.silver.employees;

-- Funciones de orden superior
SELECT
  employee_id,
  transform(skills, s -> upper(s)) AS skills_upper,
  filter(skills, s -> s ILIKE '%sql%') AS sql_skills
FROM zenviro.silver.employees;
```

## Gold — JOIN y agregados
```sql
CREATE OR REPLACE TABLE zenviro.gold.gasto_por_ubicacion AS
SELECT
  l.city, l.country,
  COUNT(DISTINCT e.employee_id) AS num_empleados,
  ROUND(SUM(jp.salary), 2) AS gasto_salarial
FROM zenviro.silver.employees e
JOIN zenviro.silver.job_positions jp ON e.job_position_id = jp.job_position_id
JOIN zenviro.bronze.departments d      ON e.department_id = d.department_id
JOIN zenviro.bronze.locations l       ON d.location_id = l.location_id
GROUP BY l.city, l.country
ORDER BY gasto_salarial DESC;
```

## UDF
```sql
CREATE OR REPLACE FUNCTION zenviro.silver.norm_name(s STRING)
RETURNS STRING RETURN upper(trim(s));

SELECT zenviro.silver.norm_name(name) FROM zenviro.silver.employees;
```

## Puntos de examen
- `from_json` + `schema_of_json` para parsear strings JSON.
- `explode` para arrays; funciones de orden superior con lambdas `x -> ...`.
- Bronze = limpieza, Silver = modelo conformado, Gold = agregados de negocio.
