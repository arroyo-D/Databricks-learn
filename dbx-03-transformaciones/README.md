# Ejercicio 3 — Transformaciones Bronze → Silver → Gold

## Objetivo
Limpiar, estandarizar y enriquecer los datos de Zenviro siguiendo la arquitectura Medallón.

## Tareas

### Bronze (limpieza y estandarización)
- [ ] Transformar `locations`: tipos correctos, columnas renombradas, fecha de ingesta
- [ ] Transformar `departments`, `attendances`, `benefits`, `trainings`, `photochecks`
- [ ] Guardar como tablas Delta en `zenviro.bronze`

### Tareas del curso
- [ ] Transformar `employment_histories`, `employee_benefits`, `employee_training`, `pictures`

### Silver (parseo y modelado)
- [ ] `employees`: string JSON → struct con `from_json` + schema explícito
- [ ] `employees`: expandir arrays con `explode` / funciones de orden superior
- [ ] `job_positions`: parsear JSON anidado (tarea)
- [ ] Guardar en `zenviro.silver`

### Gold (analítica)
- [ ] JOIN empleados ↔ posiciones ↔ departamentos ↔ ubicaciones
- [ ] Resumen: **gasto por ubicación** (agregación)
- [ ] Tarea: **beneficios por empleado**
- [ ] Guardar en `zenviro.gold`

### Extra
- [ ] Crear una UDF (p. ej. normalizar nombres a mayúsculas) y registrarla
- [ ] Usar al menos 2 funciones de orden superior (`transform`, `filter`, `exists`)

## Entregable
Notebooks por capa en `notebooks/bronze/`, `notebooks/silver/`, `notebooks/gold/`.
