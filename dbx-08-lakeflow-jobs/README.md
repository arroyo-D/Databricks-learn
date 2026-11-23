# Ejercicio 8 — Lakeflow Jobs / Workflows

## Objetivo
Orquestar los notebooks del proyecto Zenviro como un Job con varias tareas, schedule y manejo de errores.

## Tareas

### 1. Preparar notebooks
- [ ] Tener los notebooks de carga (raw), bronze, silver y gold parametrizados con widgets (`dbutils.widgets`)

### 2. Crear el Job
- [ ] Crear job `zenviro_etl` con tareas encadenadas:
  1. `cargar_raw`
  2. `bronze` (depende de cargar_raw)
  3. `silver` (depende de bronze)
  4. `gold` (depende de silver)
- [ ] Elegir compute: **job cluster** (nuevo, se apaga al terminar)
- [ ] Pasar parámetros a las tareas

### 3. Ejecutar y monitorear
- [ ] Lanzar "Run now" y seguir la ejecución en la UI de Jobs
- [ ] Revisar tiempos, logs y salida de cada tarea

### 4. Schedule
- [ ] Programar el job (cron): p. ej. cada día a las 06:15
- [ ] Pausar y reanudar el schedule

### 5. Depuración
- [ ] Introducir un error a propósito en la tarea `silver`
- [ ] Localizar el fallo en la UI, leer el log, arreglar y usar **Repair run** para reejecutar solo desde la tarea fallida

### 6. Notificaciones
- [ ] Configurar notificación por email on failure

## Entregable
Definición del job exportada (JSON/YAML si es posible) en `jobs/` + capturas en `docs/`.
