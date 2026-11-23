# Solución — Ejercicio 1

## 1-2. Metastore, credential y external location (UI)
1. Account Console → Data → Metastores → Create metastore (misma región, storage root dedicado).
2. Asignar: metastore → Workspaces → habilitar tu workspace.
3. Catalog → External Data → Create Storage Credential (Managed Identity del conector de acceso de Azure Databricks, con rol *Storage Blob Data Contributor* en ADLS).
4. Catalog → External Data → Create External Location:
   - URL: `abfss://<contenedor>@<storage>.dfs.core.windows.net/`
   - Storage credential: el creado arriba.
5. Test connection para validar.

## 3. Catálogo y schemas

```sql
CREATE CATALOG IF NOT EXISTS zenviro;

CREATE SCHEMA IF NOT EXISTS zenviro.raw;
CREATE SCHEMA IF NOT EXISTS zenviro.bronze;
CREATE SCHEMA IF NOT EXISTS zenviro.silver;
CREATE SCHEMA IF NOT EXISTS zenviro.gold;

SHOW CATALOGS;
SHOW SCHEMAS IN zenviro;
```

Con volumen apuntando a la external location:
```sql
CREATE VOLUME IF NOT EXISTS zenviro.raw.data
-- ruta resultante: /Volumes/zenviro/raw/data
```

## 4. Permisos

```sql
GRANT USE CATALOG ON CATALOG zenviro TO `tu_usuario@correo.com`;
GRANT USE SCHEMA, CREATE TABLE ON SCHEMA zenviro.bronze TO `tu_usuario@correo.com`;
SHOW GRANTS ON SCHEMA zenviro.bronze;
REVOKE CREATE TABLE ON SCHEMA zenviro.bronze FROM `tu_usuario@correo.com`;
```

## Puntos de examen
- Jerarquía de 3 niveles: `catalog.schema.table`.
- Storage Credential ≠ External Location: credencial = *cómo* se accede; location = *dónde*.
- Modo de acceso del cluster: Single user / Shared (Unity Catalog exige runtime y modo compatibles).
- El metastore se crea **uno por región** y puede compartirse entre workspaces.
