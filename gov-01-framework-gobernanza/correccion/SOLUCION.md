# Solución — GOV-01

## 1. Definición (ejemplo)
> Gobernanza de datos en Zenviro: el conjunto de personas, procesos y tecnología que garantiza que los datos de empleados sean correctos, seguros, accesibles para quien los necesita y usados conforme a la normativa.

## 2. Comparativa de frameworks
| Framework | Para quién | Fortalezas | Debilidades |
|---|---|---|---|
| DAMA-DMBOK | Orgs que quieren cobertura total | 11 áreas de conocimiento, vocabulario común | Pesado, lento de arrancar |
| DCAM | Finanzas/regulado | Medición de madurez objetiva | Coste, orientado a auditoría |
| Dominios / Data Mesh | Orgs distribuidas, escala | Ownership por dominio, federado | Exige madurez técnica previa |

Elección ejemplo: **por dominios** — Zenviro ya separa HR/finance/ops y encaja con catálogos UC por dominio.

## 3. Mapeo a Unity Catalog
| Framework | Unity Catalog |
|---|---|
| Dominio de datos | Catalog (hr, finance, ops) |
| Data owner | owner del catálogo/schema |
| Data steward | grupo con MODIFY en su dominio |
| Clasificación | tags (pii, sensitivity, domain) |
| Políticas de acceso | GRANT/REVOKE, row filters, column masks |
| Auditoría | system.access.audit |
| Trazabilidad | Data Lineage |

## 4. Inventario
```python
for c in spark.sql("SHOW CATALOGS").collect():
    print(c.catalog)
df = spark.sql("""
  SELECT table_catalog, table_schema, table_name, table_type
  FROM system.information_schema.tables
  WHERE table_catalog NOT IN ('system', 'samples')
""")
display(df)
```
Clasifica: sin owner/comentario/tags → huérfano; con dominio + owner + tags → gobernado.

Estructura propuesta: `hr`, `finance`, `ops` (catálogos) × schemas `raw/bronze/silver/gold`.

## Puntos clave
- Gobernanza = decisión organizativa primero, tecnología después.
- El framework se elige según tamaño, regulación y madurez — no hay uno "mejor".
- UC materializa el framework: cada concepto tiene su mecanismo.
