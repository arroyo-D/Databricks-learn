# Ejercicio 12 — Delta Sharing y Lakehouse Federation

## Objetivo
Compartir datos de forma segura fuera de tu organización (Delta Sharing) y consultar bases de datos externas sin mover datos (Lakehouse Federation).

## Parte A — Delta Sharing

### Conceptos
- **Provider**: quien comparte. **Recipient**: quien recibe. **Share**: conjunto de tablas compartidas.
- Protocolo abierto: el receptor NO necesita Databricks.

### Tareas
- [ ] Crear un **Share** con las tablas `zenviro.gold`
- [ ] Crear un **Recipient** (Databricks-to-Databricks)
- [ ] Conceder acceso del share al recipient
- [ ] Simular el consumo: leer el share desde otro metastore/catálogo (o explicar el flujo con el activation link)
- [ ] Teoría: ¿cómo consumiría el share una herramienta no-Databricks (pandas/Power BI)?

## Parte B — Lakehouse Federation

### Conceptos
- Consultar MySQL/PostgreSQL/SQL Server/Snowflake... como catálogo externo, sin ETL
- **Connection** + **Foreign Catalog**

### Tareas
- [ ] Crear una **Connection** a una base externa (p. ej. PostgreSQL de prueba)
- [ ] Crear el **Foreign Catalog** y consultar sus tablas con SQL
- [ ] Hacer un JOIN entre tabla federada y tabla Delta local
- [ ] Reflexión: ¿cuándo federar y cuándo ingerir?

## Entregable
Notebook `notebooks/12_sharing_federation.sql` + nota `docs/federar_vs_ingerir.md`.
