# Solución — Ejercicio 8

## Widgets en los notebooks (parametrización)
```python
dbutils.widgets.text("env", "dev", "Entorno")
dbutils.widgets.text("base_path", "/Volumes/zenviro/raw/data", "Ruta base")
env = dbutils.widgets.get("env")
```

## Job: estructura
Workflows → Jobs → Create job:
- Task 1 `cargar_raw`: notebook de ingesta
- Task 2 `bronze`: Depends on `cargar_raw`
- Task 3 `silver`: Depends on `bronze`
- Task 4 `gold`: Depends on `silver`
- Compute: New job cluster (single node, runtime LTS)
- Parameters por tarea: `{"env": "dev", "base_path": "..."}`

## Schedule
En el job → Add trigger → Scheduled:
- Cron: `0 15 6 * * ?` (06:15 diario) con timezone Europe/Madrid.

## Repair run
Cuando una tarea falla:
1. Runs → abrir la ejecución fallida.
2. Leer el error en la tarea (stderr del notebook).
3. Corregir el notebook.
4. Botón **Repair run**: reejecuta solo las tareas fallidas y sus dependientes, reutilizando los resultados de las exitosas.

## Puntos de examen
- Job cluster: se crea al lanzar y se destruye al terminar (más barato para producción).
- Las dependencias entre tareas forman un DAG; tareas sin dependencia pueden correr en paralelo.
- `dbutils.widgets` es la vía estándar de pasar parámetros a notebooks en jobs.
- Repair run evita reejecutar todo el pipeline tras un fallo parcial.
- Schedules en cron Quartz: `seg min hora día mes día_semana año?`.
