# Preguntas de práctica — dbx-00 / Dominio: Databricks Intelligence Platform (6%)

> Preguntas originales estilo examen. Soluciones al final. Hazlas sin mirar.

**P1.** ¿Qué componente de la arquitectura Spark coordina la ejecución y mantiene el plan de ejecución?
- A) El executor
- B) El driver
- C) El worker node
- D) El cluster manager

**P2.** Un job cluster se diferencia de un all-purpose cluster en que:
- A) Solo ejecuta SQL
- B) Se crea al lanzar el job y termina al acabar, reduciendo coste
- C) No soporta notebooks
- D) Es siempre serverless

**P3.** En un notebook, `%sh ls /` lista:
- A) El DBFS root
- B) El sistema de ficheros local del driver
- C) Los volúmenes de Unity Catalog
- D) El workspace de notebooks

**P4.** ¿Qué comando mágico permite ejecutar otro notebook desde el actual?
- A) `%run`
- B) `%exec`
- C) `%notebook`
- D) `%include`

**P5.** La arquitectura Medallón ordena las capas como:
- A) Gold → Silver → Bronze
- B) Bronze → Gold → Silver
- C) Bronze → Silver → Gold
- D) Raw → Gold → Silver

**P6.** `dbutils.widgets.get("env")` sirve para:
- A) Crear variables de entorno del cluster
- B) Leer el valor de un parámetro de entrada del notebook
- C) Obtener la versión del runtime
- D) Leer un secret

---

## Soluciones
1. **B** — El driver crea el plan, coordina tareas y recoge resultados; los executors ejecutan.
2. **B** — Job cluster = efímero por ejecución (más barato); all-purpose = interactivo y compartido.
3. **B** — `%sh` corre en el filesystem local del driver, NO en DBFS (eso es `%fs`).
4. **A** — `%run` ejecuta otro notebook en el mismo contexto (comparte variables).
5. **C** — Bronze (crudo) → Silver (limpio/conformado) → Gold (agregado de negocio).
6. **B** — Los widgets parametrizan notebooks; esencial en jobs.
