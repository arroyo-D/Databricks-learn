# Ejercicio 11 — Gobernanza con Unity Catalog

## Objetivo
Practicar el control y la gobernanza de datos: descubrimiento, auditoría, linaje y control de acceso.

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

## Entregable
Notebook `notebooks/11_gobernanza.sql` con los GRANT/REVOKE + capturas del linaje en `docs/`.
