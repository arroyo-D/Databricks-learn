# Solución — GOV-06

## 1. Retención
```sql
DELETE FROM zenviro.raw.events WHERE ingestion_date < add_months(current_date(), -24);
VACUUM zenviro.raw.events;
```
Programado como job semanal (dbx-08). Documentar retención por capa en el diccionario.

## 3. Seguridad: masks y row filters
```sql
-- Column mask para email
CREATE OR REPLACE FUNCTION zenviro.silver.mask_email(email STRING)
RETURN CASE WHEN is_member('hr_stewards') THEN email ELSE '***@***' END;

ALTER TABLE zenviro.silver.employees ALTER COLUMN email
  SET MASK zenviro.silver.mask_email;

-- Row filter: solo activos
CREATE OR REPLACE FUNCTION zenviro.silver.only_active(status STRING)
RETURN is_member('hr_stewards') OR status = 'active';

ALTER TABLE zenviro.silver.employees
  SET ROW FILTER zenviro.silver.only_active ON (status);
```

## 4. Impact analysis
En Catalog Explorer → tabla → Lineage: downstream (queries, dashboards, jobs). Cambiar una columna de bronze afecta a todo lo que cuelga del grafo. El linaje responde "¿qué se rompe?" antes de tocar.

## 5. Drift
```sql
WITH hist AS (
  SELECT AVG(importe) AS media_hist, STDDEV(importe) AS std_hist
  FROM zenviro.gold.ventas_diarias
  WHERE fecha BETWEEN add_months(current_date(),-2) AND add_months(current_date(),-1)
), act AS (
  SELECT AVG(importe) AS media_actual
  FROM zenviro.gold.ventas_diarias
  WHERE fecha >= add_months(current_date(),-1)
)
SELECT media_actual, media_hist,
       ABS(media_actual-media_hist)/media_hist AS desviacion
FROM hist CROSS JOIN act;
-- Alerta (dbx-10): desviacion > 0.3 -> notificar
```

## 6. GDPR — derecho al olvido
```sql
DELETE FROM zenviro.silver.employees WHERE employee_id = 123;
DELETE FROM zenviro.bronze.employees WHERE employee_id = 123;
-- + purge del historial Delta si aplica:
VACUUM zenviro.silver.employees RETAIN 0 HOURS;
```
Documentar el procedimiento completo: identificar todas las tablas con la persona (el diccionario + tags PII lo hacen posible), borrar, auditar (system.access.audit).

## Puntos clave
- Gobernanza en producción = jobs + dashboards + alertas, no documentos.
- Column masks y row filters aplican la política en el motor, no en cada app.
- El linaje convierte el impact analysis de adivinanza en consulta.
- Sin tags PII y diccionario, el derecho al olvido es imposible de ejecutar con garantías.
