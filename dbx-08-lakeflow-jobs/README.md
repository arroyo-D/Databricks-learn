# Ejercicio 8 — Lakeflow Jobs / Workflows

## Objetivo
Orquestar los notebooks del proyecto Zenviro como un Job con varias tareas, schedule y manejo de errores.


## Por qué importa

Un pipeline que solo corre cuando tú le das a "Run all" no es un pipeline: es un cuaderno. Lakeflow Jobs (16% del examen) es la capa de orquestación: tareas con dependencias, reintentos, timeouts, notificaciones y programación. Es la diferencia entre "juguete" y "producción", y la mitad de las preguntas de troubleshooting del examen viven aquí.

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

## Criterios de éxito

- [ ] Job multi-tarea con dependencias correctas (no todo secuencial si hay paralelismo).
- [ ] Política de reintentos y timeout configurada y justificada.
- [ ] Alerta de fallo recibida (email/webhook) tras provocar un error.

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Lakeflow Jobs*)

## Entregable
Definición del job exportada (JSON/YAML si es posible) en `jobs/` + capturas en `docs/`.