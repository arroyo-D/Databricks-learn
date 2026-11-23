# GOV-03 — Consejo de Gobernanza y Diccionario de Datos

> Capítulo 3 de Maffeo: council, dominios de datos y diccionario de datos — materializado con tags y comentarios de Unity Catalog.

## Objetivo
Crear el artefacto más útil de cualquier programa de gobernanza: un diccionario de datos vivo dentro de UC.


## Por qué importa

El diccionario de datos es el artefacto más útil y menos valorado: una definición acordada por campo de negocio ("¿qué es exactamente un 'cliente activo'?"). En Databricks vive en comentarios de tablas y columnas de UC, etiquetas y el buscador de Catalog. Este módulo lo crea de verdad y monta el consejo que lo mantiene vivo.

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

## Criterios de éxito

- [ ] 10+ términos de negocio definidos, aprobados y escritos como comentarios UC.
- [ ] Etiquetas (tags) de sensibilidad aplicadas a columnas con PII sintética.
- [ ] Acta de una reunión de consejo (asistentes, decisiones, conflictos resueltos).

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Data governance with Unity Catalog*)

## Entregable
`docs/consejo.md` + notebook `notebooks/gov03_diccionario.sql` + tabla `data_dictionary`.