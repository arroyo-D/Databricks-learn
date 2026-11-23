# Ejercicio 11 — Gobernanza con Unity Catalog

## Objetivo
Practicar el control y la gobernanza de datos: descubrimiento, auditoría, linaje y control de acceso.


## Por qué importa

La gobernanza es el **15% del examen** y el requisito nº1 de las empresas para poner IA en producción (según el informe *State of AI Agents*, las que gobiernan activamente llevan 12x más proyectos a producción). Aquí pasas de la teoría (track gov) a la práctica: permisos finos, row filters, column masks, linaje y auditoría en Unity Catalog.

## Conceptos
- Sin UC: permisos por tabla en cada workspace, sin visibilidad global
- Con UC: gobierno centralizado, linaje automático, auditoría

## Tareas

### 1. Data Discovery
- [ ] Usar el **Catalog Explorer**: buscar tablas por nombre, filtrar por catálogo/schema/owner
- [ ] Añadir comentarios y tags a tablas y columnas

### 2. Data Lineage
- [ ] Ejecutar un flujo raw→bronze→silver→gold y abrir el **linaje** de una tabla gold
- [ ] Identificar: tablas upstream, notebooks/jobs que las producen

### 3. Data Audit
- [ ] Localizar las tablas de sistema `system.access.audit` (si están habilitadas)
- [ ] Consultar quién accedió a qué tabla y cuándo

### 4. Data Access Control
- [ ] Crear un grupo `data_analysts` (Account Console)
- [ ] `GRANT SELECT ON SCHEMA zenviro.gold TO data_analysts`
- [ ] Denegar acceso a silver/bronze y verificar con otro usuario o `SHOW GRANTS`
- [ ] Column-level: dynamic view que enmascare emails (o row filter / column mask si tu edición lo permite)
- [ ] Propietarios: cambiar el owner de un schema

## Criterios de éxito

- [ ] Row filter y column mask aplicados a una tabla con datos "sensibles" sintéticos.
- [ ] Linaje de una tabla Gold rastreado hasta su origen en la UI de Catalog.
- [ ] Auditoría: responder "¿quién accedió a esta tabla esta semana?" con system tables.

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Data governance with Unity Catalog*)

## Entregable
Notebook `notebooks/11_gobernanza.sql` con los GRANT/REVOKE + capturas del linaje en `docs/`.