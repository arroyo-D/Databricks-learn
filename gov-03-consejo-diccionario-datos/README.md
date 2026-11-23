# GOV-03 — Consejo de Gobernanza y Diccionario de Datos

> Capítulo 3 de Maffeo: council, dominios de datos y diccionario de datos — materializado con tags y comentarios de Unity Catalog.

## Objetivo
Crear el artefacto más útil de cualquier programa de gobernanza: un diccionario de datos vivo dentro de UC.

## Tareas

### 1. Consejo (documento)
- [ ] `docs/consejo.md`: sponsor, miembros, cadencia de reuniones y 5 principios clave para Zenviro

### 2. Dominios de datos
- [ ] Define los dominios de Zenviro (hr, finance, ops...) y qué tablas caen en cada uno
- [ ] Refleja los dominios con tags: `ALTER TABLE ... SET TAGS ('domain'='hr')`

### 3. Diccionario de datos
- [ ] Para las 5 tablas principales: comentario de tabla + comentario de CADA columna (significado de negocio, no "string que viene del API")
- [ ] Clasifica columnas sensibles: tag `pii=true` en emails, nombres, salarios
- [ ] Verifica en Catalog Explorer que se ve todo

### 4. Diccionario consultable
- [ ] Query sobre `system.information_schema.columns` que exporte el diccionario completo (tabla, columna, tipo, comentario, tags)
- [ ] Guárdalo como tabla Delta `zenviro.governance.data_dictionary` (un diccionario QUE TAMBIÉN ES DATO)

### 5. Detección de PII
- [ ] Escribe una función que escanee nombres de columna buscando patrones PII (email, phone, salary, dni...) y proponga tags

## Entregable
`docs/consejo.md` + notebook `notebooks/gov03_diccionario.sql` + tabla `data_dictionary`.
