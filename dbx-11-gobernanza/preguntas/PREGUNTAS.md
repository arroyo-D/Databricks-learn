# Preguntas de práctica — dbx-11 / Dominio: Governance & Security (15%)

**P1.** `GRANT SELECT ON SCHEMA gold TO g` concede:
- A) SELECT solo en tablas futuras
- B) SELECT en todas las tablas del schema (presentes y futuras)
- C) SELECT solo en la tabla indicada
- D) Nada sin USE SCHEMA

**P2.** Para consultar una tabla hacen falta como mínimo:
- A) SELECT
- B) USE CATALOG + USE SCHEMA + SELECT
- C) MODIFY
- D) OWNERSHIP

**P3.** El data lineage en UC:
- A) Hay que configurarlo tabla a tabla
- B) Se captura automáticamente al ejecutar queries en compute con UC (nivel tabla y columna)
- C) Solo funciona con Delta Sharing
- D) Requiere una licencia aparte

**P4.** Una vista dinámica con `is_member('grupo')` permite:
- A) Crear usuarios
- B) Mostrar datos distintos (p. ej. enmascarados) según el grupo del que consulta
- C) Acelerar queries
- D) Saltarse los permisos

**P5.** La auditoría de accesos en UC se consulta en:
- A) `system.access.audit` (system tables)
- B) El driver log
- C) No existe auditoría
- D) El transaction log de Delta

**P6.** ¿Quién puede cambiar el owner de un schema?
- A) Cualquiera con SELECT
- B) El owner actual o un metastore admin, vía `ALTER SCHEMA ... OWNER TO`
- C) Solo soporte de Databricks
- D) Nadie, es inmutable

**P7.** El privilege `MODIFY` sobre una tabla permite:
- A) Solo SELECT
- B) INSERT/UPDATE/DELETE/MERGE sobre los datos
- C) Cambiar el owner
- D) Gestionar permisos

---

## Soluciones
1. **B** — Los grants a nivel schema heredan a todas las tablas, incluidas las futuras.
2. **B** — La triada USE CATALOG + USE SCHEMA + SELECT cae seguro en el examen.
3. **B** — Automático, a nivel tabla Y columna; lo verás en la pestaña Lineage.
4. **B** — Base del enmascaramiento dinámico (antes de column masks, y aún válido).
5. **A** — System tables de la cuenta; hay que habilitarlas en el Account Console.
6. **B** — Ownership se transfiere con ALTER ... OWNER TO.
7. **B** — MODIFY = escritura de datos, no de metadatos ni permisos.
