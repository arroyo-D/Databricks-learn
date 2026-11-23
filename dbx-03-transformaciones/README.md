# Ejercicio 3 — Transformaciones Bronze → Silver → Gold

## Objetivo
Limpiar, estandarizar y enriquecer los datos de Zenviro siguiendo la arquitectura Medallón.


## Por qué importa

Las transformaciones son el dominio con más peso del examen: **22%**. Este módulo es el corazón de la arquitectura medallón: convertir Bronze (crudo) en Silver (limpio, deduplicado, tipado) y Silver en Gold (modelado para negocio). Las decisiones que tomes aquí —qué reglas de calidad, qué granularidad, qué claves— son exactamente las que se evalúan en el examen y en el trabajo real.

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

## Criterios de éxito

- [ ] Silver con deduplicación justificada (qué clave, qué regla de "gana el más reciente").
- [ ] Gold con granularidad explícita y al menos 3 métricas de negocio.
- [ ] Puedes explicar la diferencia entre transformación narrow y wide (shuffles).

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Data engineering*)

## Entregable
Notebooks por capa en `notebooks/bronze/`, `notebooks/silver/`, `notebooks/gold/`.