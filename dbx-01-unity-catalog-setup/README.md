# Ejercicio 1 — Configuración de Unity Catalog

## Objetivo
Configurar la gobernanza desde cero: metastore, credenciales de almacenamiento, external locations y catálogo del proyecto.

## Conceptos (leer del PDF)
- Modelo de objetos: **Metastore → Catalog → Schema → Table/View/Function/Volume**
- Metastore = contenedor de nivel superior por región
- Storage Credential + External Location = acceso gobernado al Data Lake (ADLS Gen2)

## Tareas

### 1. Metastore
- [ ] Acceder al **Account Console** (Manage Account)
- [ ] Crear el metastore en la misma región que el workspace
- [ ] Asignar el metastore al workspace

### 2. Acceso al Data Lake
- [ ] Crear un **Storage Credential** (Managed Identity o Service Principal con permisos sobre ADLS)
- [ ] Crear una **External Location** apuntando al contenedor del Data Lake
- [ ] Validar la conexión (botón Test / validación)

### 3. Estructura del proyecto
- [ ] Crear el catálogo `zenviro` y los schemas `raw`, `bronze`, `silver`, `gold`
- [ ] Configurar el cluster para Unity Catalog (modo de acceso: Single user o Shared con runtime compatible)
- [ ] Verificar con `SHOW CATALOGS` y `SHOW SCHEMAS IN zenviro`

### 4. Permisos
- [ ] Conceder `USE CATALOG` y `USE SCHEMA` a tu usuario
- [ ] Probar `GRANT SELECT` / `REVOKE` sobre un schema

## Entregable
Notebook `notebooks/01_unity_catalog.sql` (o `.py` con `%sql`) con todos los comandos ejecutados.

## Nota
Si usas Community Edition no hay Unity Catalog completo: haz la parte teórica y usa el catálogo `hive_metastore` / `workspace`.
