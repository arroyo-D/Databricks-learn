# Preguntas de práctica — dbx-12 / Dominio: Governance & Security (Sharing/Federation)

**P1.** Delta Sharing permite compartir datos con receptores que:
- A) Deben usar Databricks obligatoriamente
- B) Pueden usar cualquier herramienta compatible con el protocolo abierto (pandas, Power BI...)
- C) Deben estar en la misma región
- D) Deben tener Unity Catalog

**P2.** En Delta Sharing, un Share contiene:
- A) Workspaces completos
- B) Tablas, vistas y volúmenes seleccionados
- C) Credenciales de acceso
- D) Notebooks

**P3.** El receptor Databricks de un share:
- A) Descarga los datos a su storage
- B) Crea un catálogo a partir del provider y consulta los datos bajo demanda
- C) Recibe una copia física diaria
- D) Debe pagar por el cómputo del proveedor

**P4.** Lakehouse Federation crea:
- A) Copias de bases externas en Delta
- B) Un catálogo externo de SOLO LECTURA sobre PostgreSQL/MySQL/Snowflake... sin mover datos
- C) Un pipeline de replicación
- D) Un SQL Warehouse

**P5.** Para federar hace falta:
- A) Connection (credenciales/host) + Foreign Catalog
- B) Solo un JDBC URL en el notebook
- C) Un storage credential
- D) Delta Sharing

**P6.** ¿Cuándo preferir federación sobre ingesta?
- A) Siempre
- B) Consultas puntuales sobre datos frescos del sistema origen, sin necesidad de histórico
- C) Analítica pesada diaria
- D) Cuando el origen se va a dar de baja

---

## Soluciones
1. **B** — Protocolo abierto: el receptor no necesita Databricks.
2. **B** — Share = conjunto gobernado de objetos compartidos.
3. **B** — Acceso bajo demanda vía UC; no hay copia salvo que el receptor la materialice.
4. **B** — Read-only y sin ETL: los datos se quedan en el origen.
5. **A** — Connection + CREATE FOREIGN CATALOG.
6. **B** — Para analítica intensiva o históricos, ingiere a Delta (mejor rendimiento y control).
