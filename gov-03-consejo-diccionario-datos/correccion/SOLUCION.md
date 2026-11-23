# Solución — GOV-03

## 2. Tags de dominio
```sql
ALTER TABLE zenviro.silver.employees SET TAGS ('domain'='hr', 'sensitivity'='high');
ALTER TABLE zenviro.gold.gasto_por_ubicacion SET TAGS ('domain'='hr', 'sensitivity'='low');
```

## 3. Comentarios
```sql
COMMENT ON TABLE zenviro.silver.employees IS
  'Empleados activos e históricos de Zenviro. Fuente: HRIS. Actualización diaria.';

ALTER TABLE zenviro.silver.employees ALTER COLUMN email
  COMMENT 'Correo corporativo. PII. Formato: nombre.apellido@zenviro.com';
```

## 4. Diccionario como dato
```sql
CREATE SCHEMA IF NOT EXISTS zenviro.governance;

CREATE OR REPLACE TABLE zenviro.governance.data_dictionary AS
SELECT c.table_schema, c.table_name, c.column_name, c.data_type,
       c.comment,
       t.comment AS table_comment
FROM system.information_schema.columns c
JOIN system.information_schema.tables t
  ON c.table_catalog = t.table_catalog
 AND c.table_schema  = t.table_schema
 AND c.table_name    = t.table_name
WHERE c.table_catalog = 'zenviro'
ORDER BY c.table_schema, c.table_name, c.ordinal_position;
```

## 5. Detector de PII
```python
import re
PII_PATTERNS = {
    "email":  r"(?i)e?mail",
    "phone":  r"(?i)phone|tel|movil",
    "salary": r"(?i)salary|sueldo|salario|importe",
    "id_nat": r"(?i)dni|nif|ssn|pasaporte",
    "name":   r"(?i)^name$|nombre|apellido",
}
cols = spark.sql("""
  SELECT table_name, column_name FROM system.information_schema.columns
  WHERE table_catalog='zenviro'""").collect()
for r in cols:
    for tipo, pat in PII_PATTERNS.items():
        if re.search(pat, r.column_name):
            print(f"{r.table_name}.{r.column_name} -> {tipo}")
```

## Puntos clave
- El diccionario vive EN el catálogo (comentarios/tags), no en un Excel muerto.
- Comentario bueno = significado de negocio + fuente + frecuencia, no la descripción técnica obvia.
- information_schema convierte el diccionario en consultable y automatizable.
