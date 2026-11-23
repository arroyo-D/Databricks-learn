# GOV-06 — Monitorización de Datos en Producción

> Capítulo 6 de Maffeo: gobernar el ciclo de vida completo, data mesh, seguridad, linaje, drift y regulación.

## Objetivo
Poner en producción todo lo aprendido: monitorización automática de calidad, seguridad y linaje.


## Por qué importa

La gobernanza no termina en el despliegue: hay que vigilar calidad, uso y coste en producción. Este módulo usa las system tables de Databricks (accesos, jobs, billing) y Lakehouse Monitoring para responder: ¿quién usa qué?, ¿qué pipeline se degrada?, ¿dónde se gasta de más? Cierra el ciclo del framework del gov-01.

## Tareas

### 1. Gobernanza del ciclo de vida
- [ ] Define políticas de retención por capa (raw: 2 años, bronze: 1 año...) y documéntalas
- [ ] Implementa el borrado programado de datos caducados (job con `DELETE WHERE` + VACUUM)

### 2. Automatizar la gobernanza federada
- [ ] Job semanal que ejecute la auditoría de GOV-04 y guarde histórico
- [ ] Dashboard de salud de gobernanza (% documentado, % con owner, incidencias)

### 3. Seguridad
- [ ] Aplica column masks a PII: email y salario visibles solo para `hr_stewards`
- [ ] Row filter: analysts solo ven empleados activos
- [ ] Verifica el resultado consultando como distinto rol (o con `SHOW GRANTS`)

### 4. Linaje en producción
- [ ] Captura el grafo de linaje completo de una tabla gold hasta las fuentes
- [ ] Usa el linaje para un impact analysis: "si cambio la columna X de bronze, ¿qué se rompe?"

### 5. Data drift
- [ ] Compara la distribución de una métrica clave este mes vs el anterior (comparación de percentiles o test KS)
- [ ] Define una alerta: si la media diaria se desvía > 30% de la media histórica → alerta

### 6. Despliegue como unidad + regulación
- [ ] Cada tabla nueva = unidad desplegable (DDL + docs + expectations + tags) — revisa tu flujo de GOV-05
- [ ] `docs/regulacion.md`: qué cambios regulatorios afectarían a Zenviro (GDPR: derecho al olvido → cómo borrarías a un empleado de todas las capas)

## Criterios de éxito

- [ ] 3 queries sobre system tables respondiendo preguntas de gobierno reales.
- [ ] Monitor de calidad activo sobre una tabla con alerta configurada.
- [ ] Informe mensual de salud de datos (plantilla reutilizable) generado.

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Administration* → system tables; *Lakehouse Monitoring*)

## Entregable
Job de auditoría + `docs/` (retencion, regulacion) + notebook `notebooks/gov06_seguridad_drift.sql`.