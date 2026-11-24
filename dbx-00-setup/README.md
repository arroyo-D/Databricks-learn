# Ejercicio 0 — Setup y Fundamentos de Databricks

> Curso: Databricks Certified Data Engineer Associate (Udatademy)

## Objetivo
Dejar el entorno listo y dominar lo básico: workspace, clusters, notebooks, comandos mágicos, DBFS y dbutils.


## Por qué importa

Sin workspace no hay proyecto: este módulo deja tu entorno listo y cubre los conceptos de plataforma que son el **6% del examen** (dominio *Intelligence Platform*): diferencia entre Data Warehouse, Data Lake y Lakehouse, arquitectura medallón y cómo ejecuta Spark (driver, executors, slots). Son las preguntas "de cultura general" del examen y de cualquier entrevista técnica de datos.

## Tareas

### 1. Teoría mínima (leer del PDF)
- Data Warehouse vs Data Lake vs Data Lakehouse
- Arquitectura Medallón (Bronze / Silver / Gold)
- Arquitectura de Apache Spark: Driver, Executors, Slots

### 2. Workspace y Cluster
- [ ] Acceder a tu workspace de **Databricks Free Edition** (NO crees Azure Databricks de pago: Free Edition ya te da todo lo necesario)
- [ ] **Adaptación Free Edition**: no hay clusters clásicos — el compute serverless se adjunta solo al ejecutar la primera celda. Localiza dónde se ve el compute conectado en la UI del notebook
- [ ] Estudia igualmente la teoría de clusters (all-purpose vs job, single node, auto-termination, Databricks Runtime LTS) con el PDF: **cae en el examen** aunque en Free Edition no los crees
- [ ] Identificar en la UI: Compute, Workspace, Catalog, Workflows, SQL

> 📖 Lee antes de nada [docs/recursos-gratuitos.md](../docs/recursos-gratuitos.md): límites de Free Edition (cuota diaria, salida a Internet restringida) que condicionan todo el programa.

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

## Criterios de éxito

- [ ] Cluster creado, usado y auto-terminado (no dejado encendido).
- [ ] Notebook con celdas en Python y SQL ejecutadas, y los 6 comandos mágicos probados.
- [ ] Puedes explicar con tus palabras qué es un lakehouse y qué hace el driver de Spark.

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Get started tutorials*, *Workspace UI*)

## Entregable
Notebook(s) exportados a `notebooks/` + capturas en `docs/`.