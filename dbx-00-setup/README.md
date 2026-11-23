# Ejercicio 0 — Setup y Fundamentos de Databricks

> Curso: Databricks Certified Data Engineer Associate (Udatademy)

## Objetivo
Dejar el entorno listo y dominar lo básico: workspace, clusters, notebooks, comandos mágicos, DBFS y dbutils.

## Tareas

### 1. Teoría mínima (leer del PDF)
- Data Warehouse vs Data Lake vs Data Lakehouse
- Arquitectura Medallón (Bronze / Silver / Gold)
- Arquitectura de Apache Spark: Driver, Executors, Slots

### 2. Workspace y Cluster
- [ ] Crear el recurso Azure Databricks (o acceder a tu workspace)
- [ ] Crear un **cluster all-purpose**: single node, Databricks Runtime LTS, terminar a los 30 min de inactividad
- [ ] Identificar en la UI: Compute, Workspace, Catalog, Workflows, SQL

### 3. Notebooks
- [ ] Crear notebook `00_hola_databricks` en tu carpeta de usuario
- [ ] Ejecutar celdas en Python y en SQL (cambia el lenguaje por celda)
- [ ] Probar los comandos mágicos: `%sql`, `%python`, `%fs`, `%sh`, `%md`, `%run`

### 4. DBFS y dbutils
- [ ] Listar el DBFS root con `%fs ls /` y con `dbutils.fs.ls("/")`
- [ ] Crear un fichero de texto en DBFS y leerlo
- [ ] Crear un **widget** de texto en un notebook, leerlo con `dbutils.widgets.get()` y eliminarlo
- [ ] Explorar `dbutils.secrets` (solo listar scopes, no crear)

### 5. Debug
- [ ] Provocar un error a propósito en una celda y localizar el traceback
- [ ] Usar el debugger del notebook con un breakpoint

## Entregable
Notebook(s) exportados a `notebooks/` + capturas en `docs/`.
