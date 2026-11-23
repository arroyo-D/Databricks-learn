# Preguntas de práctica — dbx-09 / Dominio: Lakeflow Jobs + Ingestion (SDP)

**P1.** Una streaming table en Lakeflow Spark Declarative Pipelines:
- A) Se recalcula entera en cada ejecución
- B) Ingiere incrementalmente: solo procesa datos nuevos
- C) Es una vista sin datos
- D) Solo puede crearse en Python

**P2.** Una materialized view en SDP:
- A) Es streaming puro
- B) Almacena el resultado de una query y se refresca según el pipeline
- C) No ocupa almacenamiento
- D) Equivale a una temp view

**P3.** Diferencia entre `@dp.expect` y `@dp.expect_or_drop`:
- A) Ninguna
- B) expect registra la violación y conserva la fila; expect_or_drop la elimina
- C) expect para la pipeline; drop solo avisa
- D) expect solo funciona en SQL

**P4.** `@dp.expect_or_fail`:
- A) Borra las filas malas
- B) Detiene el pipeline ante una violación
- C) Envía un email
- D) Ignora el error

**P5.** SCD Tipo 2 con Auto CDC (`create_auto_cdc_flow`) genera:
- A) Solo la última versión de cada registro
- B) Historial completo con columnas de vigencia (`__START_AT`/`__END_AT`)
- C) Una tabla por versión
- D) Un backup diario

**P6.** Un "full refresh" de un pipeline SDP:
- A) Solo procesa incrementos
- B) Recalcula todas las tablas desde cero
- C) Borra el pipeline
- D) Solo aplica a streaming tables

**P7.** `APPLY CHANGES INTO` / Auto CDC necesita obligatoriamente:
- A) Particionado por fecha
- B) Claves (`keys`) y una columna de secuencia (`sequence_by`)
- C) Un SQL Warehouse
- D) Liquid Clustering

---

## Soluciones
1. **B** — Streaming table = ingesta incremental (append); es el building block de bronze en SDP.
2. **B** — MV = resultado materializado refrescado por el pipeline (típico en silver/gold).
3. **B** — Tres niveles: warn (expect), drop, fail.
4. **B** — Fail fast para reglas críticas.
5. **B** — SCD2 conserva historia con vigencias; SCD1 sobrescribe.
6. **B** — Full refresh = recomputo completo; incremental = solo datos nuevos (donde aplique).
7. **B** — Sin keys ni sequence_by no hay forma de ordenar ni deduplicar cambios.
