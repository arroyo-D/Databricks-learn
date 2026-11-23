# Solución — Ejercicio 12

## Parte A — Delta Sharing
```sql
CREATE SHARE IF NOT EXISTS zenviro_share;

ALTER SHARE zenviro_share ADD TABLE zenviro.gold.gasto_por_ubicacion;
ALTER SHARE zenviro_share ADD TABLE zenviro.gold.beneficios_por_empleado;

CREATE RECIPIENT IF NOT EXISTS partner_bi;
GRANT SELECT ON SHARE zenviro_share TO RECIPIENT partner_bi;

SHOW GRANTS TO RECIPIENT partner_bi;
```
Flujo receptor Databricks: activation link → crea el provider en su metastore → `CREATE CATALOG ... USING PROVIDER` → consulta las tablas compartidas.

Receptor NO-Databricks: descarga el fichero de credenciales (activation) y usa el conector open-source:
```python
import delta_sharing
share_file = "/path/config.share"
client = delta_sharing.SharingClient(share_file)
pdf = delta_sharing.load_as_pandas(f"{share_file}#zenviro_share.zenviro.gasto_por_ubicacion")
```

## Parte B — Lakehouse Federation
```sql
CREATE CONNECTION IF NOT EXISTS pg_conn
TYPE POSTGRESQL
OPTIONS (host '...', port '5432', user '...', password '...');

CREATE FOREIGN CATALOG IF NOT EXISTS pg_catalog
USING CONNECTION pg_conn
OPTIONS (database 'appdb');

-- Consulta directa
SELECT * FROM pg_catalog.public.orders LIMIT 10;

-- JOIN federado + Delta local
SELECT o.order_id, e.name
FROM pg_catalog.public.orders o
JOIN zenviro.silver.employees e ON o.employee_id = e.employee_id;
```

## Federar vs ingerir
| Federar | Ingerir |
|---|---|
| Consultas puntuales, datos frescos | Uso frecuente/analítica pesada |
| Sin copia, sin ETL | Mejor rendimiento (Delta + cache) |
| Dependencia del sistema origen | Control total del histórico |

## Puntos de examen
- Delta Sharing comparte a nivel de tabla/vista/volumen, con gobierno UC.
- El receptor no-Databricks usa el protocolo abierto + conectores (pandas, Power BI, Tableau).
- Federation crea un catálogo externo de solo consulta (read-only).
