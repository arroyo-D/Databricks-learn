# Solución — Ejercicio 11

## 1. Discovery
Catalog → buscar "employees": filtra por catalog `zenviro`, schema, owner, tags.
```sql
COMMENT ON TABLE zenviro.gold.gasto_por_ubicacion IS 'Gasto salarial agregado por ciudad y país';
ALTER TABLE zenviro.gold.gasto_por_ubicacion SET TAGS ('domain'='hr', 'sensitivity'='medium');
```

## 2. Lineage
En Catalog Explorer, tabla gold → pestaña **Lineage**: muestra tablas upstream (silver, bronze), downstream, notebooks, jobs y dashboards que la usan. Se genera automáticamente al ejecutar queries en compute con UC.

## 3. Audit
```sql
SELECT event_time, user_identity.email, request_params.full_name_arg, action_name
FROM system.access.audit
WHERE request_params.full_name_arg LIKE 'zenviro.gold%'
ORDER BY event_time DESC
LIMIT 50;
```
(System tables deben estar habilitadas en el Account Console.)

## 4. Access control
```sql
-- Grupo creado en Account Console → después:
GRANT USE CATALOG ON CATALOG zenviro TO `data_analysts`;
GRANT USE SCHEMA ON SCHEMA zenviro.gold TO `data_analysts`;
GRANT SELECT ON SCHEMA zenviro.gold TO `data_analysts`;

SHOW GRANTS ON SCHEMA zenviro.gold;
REVOKE SELECT ON SCHEMA zenviro.gold FROM `data_analysts`;

-- Cambiar owner
ALTER SCHEMA zenviro.gold OWNER TO `data_analysts`;
```

Enmascarado con vista dinámica:
```sql
CREATE OR REPLACE VIEW zenviro.gold.employees_masked AS
SELECT employee_id,
       CASE WHEN is_member('data_engineers') THEN email
            ELSE regexp_replace(email, '(^.).*(@.*$)', '$1***$2') END AS email
FROM zenviro.silver.employees;
```

## Puntos de examen
- GRANT heredable: dar SELECT en schema da SELECT en todas sus tablas.
- Linaje y audit son automáticos con Unity Catalog (nivel tabla y columna).
- `is_member()` permite vistas dinámicas por grupo.
- Privilegios mínimos para consultar: USE CATALOG + USE SCHEMA + SELECT.
