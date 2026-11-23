# Solución — GOV-02

## 1. RACI (ejemplo hr/employees)
| Actividad | Owner HR | Steward HR | Data Engineer | Analyst |
|---|---|---|---|---|
| Definir significado de campos | A | R | I | C |
| Aprobar accesos | A | R | C | I |
| Pipeline ETL | I | C | R | I |
| Documentar tablas | C | R | C | I |

Un steward: conoce el negocio, documenta, vigila la calidad, gestiona accesos de su dominio. NO es: DBA, soporte de incidencias ni desarrollador ETL.

## 3-4. Implementación UC
```sql
-- Ownership por dominio
ALTER SCHEMA zenviro.silver OWNER TO `hr_stewards`;
DESCRIBE SCHEMA EXTENDED zenviro.silver;

-- Stewards: leer, escribir y documentar su dominio
GRANT USE CATALOG ON CATALOG zenviro TO `hr_stewards`;
GRANT USE SCHEMA, SELECT, MODIFY ON SCHEMA zenviro.silver TO `hr_stewards`;

-- Analysts: solo gold
GRANT USE SCHEMA, SELECT ON SCHEMA zenviro.gold TO `data_analysts`;

-- Engineers: gestión completa
GRANT ALL PRIVILEGES ON CATALOG zenviro TO `data_engineers`;

SHOW GRANTS ON SCHEMA zenviro.silver;
SHOW GRANTS ON CATALOG zenviro;
```

## 5. KPIs de stewardship
```sql
-- % tablas con comentario
SELECT
  ROUND(100.0 * SUM(CASE WHEN comment IS NOT NULL THEN 1 ELSE 0 END) / COUNT(*), 1) AS pct_documentadas
FROM system.information_schema.tables
WHERE table_catalog = 'zenviro';

-- % con tags
SELECT COUNT(DISTINCT table_name) AS tablas_taggeadas
FROM system.information_schema.table_tags
WHERE catalog_name = 'zenviro';

-- % columnas documentadas
SELECT table_name,
  ROUND(100.0 * SUM(CASE WHEN comment IS NOT NULL THEN 1 ELSE 0 END)/COUNT(*), 1) AS pct_cols
FROM system.information_schema.columns
WHERE table_catalog = 'zenviro'
GROUP BY table_name;
```

## Puntos clave
- Un rol sin permisos técnicos que lo respalden es teatro: steward = grupo + grants + KPIs.
- Owner a nivel schema/catálogo hereda a las tablas.
- Los KPIs de documentación convierten "gobernanza" en algo medible.
