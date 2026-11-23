# Ejercicio 9 — Lakeflow Spark Declarative Pipelines (SDP)

## Objetivo
Construir un pipeline declarativo completo (proyecto rideshare: users, drivers, vehicles, payments, trips) con streaming tables, expectations, SCD2 y vistas materializadas.

## Conceptos
- **Streaming table**: ingesta incremental (Auto Loader)
- **Materialized view**: resultado de una query, refrescado por el pipeline
- **Expectations**: calidad de datos (`@dp.expect`, `expect_or_drop`, `expect_or_fail`)
- **Auto CDC / APPLY CHANGES INTO**: SCD tipo 1 y 2

## Tareas

### 1. Configuración
- [ ] Crear el entorno del proyecto SDP (pipeline nuevo, catálogo y schema destino)
- [ ] Recorrer la interfaz de SDP: grafo, ejecuciones, eventos

### 2. Ingesta (streaming tables)
- [ ] SQL: `users` como streaming table con Auto Loader (`read_files`)
- [ ] Tarea: `drivers` en SQL
- [ ] Python: `payments` con `pyspark.pipelines` (`@dp.table`)
- [ ] Tarea: `vehicles` en Python
- [ ] Tarea: `trips` en SQL o Python

### 3. Calidad
- [ ] Expectations sobre `users`: email no nulo, edad > 0 (warn / drop / fail)
- [ ] Expectations sobre `payments`: amount > 0
- [ ] Revisar las métricas de calidad en la UI del pipeline

### 4. SCD2
- [ ] `payments`: crear tabla CDC con `dp.create_auto_cdc_flow` (o APPLY CHANGES INTO) tipo 2 con secuencia por fecha

### 5. Gold (agregados)
- [ ] Materialized view `driver_revenue_summary`
- [ ] Tarea: `payment_method_summary`

### 6. Ejecutar
- [ ] Full refresh vs refresco incremental: ¿qué tablas se recalculan?

## Entregable
Código del pipeline en `pipeline/` (SQL y Python) + captura del grafo en `docs/`.
