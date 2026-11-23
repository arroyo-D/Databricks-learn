# Preguntas de práctica — dbx-07 / Dominio: Transformation + Troubleshooting/Optimization (Delta Lake)

**P1.** Delta Lake logra transacciones ACID mediante:
- A) Locks distribuidos
- B) El transaction log (`_delta_log`) con commits atómicos
- C) Una base de datos auxiliar
- D) El driver en modo exclusivo

**P2.** `RESTORE TABLE t TO VERSION AS OF 2`:
- A) Borra las versiones 0-2
- B) Devuelve la tabla al estado de la versión 2 (creando una nueva versión)
- C) Solo funciona con tablas externas
- D) Es irreversible y destruye datos

**P3.** `VACUUM` con retención de 0 horas:
- A) Borra el transaction log
- B) Elimina físicamente archivos antiguos → el time travel anterior deja de funcionar
- C) Compacta archivos pequeños
- D) No afecta al time travel

**P4.** `COPY INTO` es idempotente porque:
- A) Sobrescribe siempre
- B) Registra los archivos ya cargados y los omite en reejecuciones
- C) Usa checksums en el driver
- D) No lo es: hay que deduplicar a mano

**P5.** Un MERGE típico de upsert incluye:
- A) Solo WHEN MATCHED
- B) WHEN MATCHED THEN UPDATE + WHEN NOT MATCHED THEN INSERT (+ opcional DELETE)
- C) WHEN NOT MATCHED BY TARGET únicamente
- D) GROUP BY

**P6.** `OPTIMIZE ... ZORDER BY (col)`:
- A) Crea un índice tipo B-tree
- B) Compacta archivos y coloca datos relacionados juntos para saltar archivos (data skipping)
- C) Ordena la tabla alfabéticamente
- D) Solo funciona con Liquid Clustering

**P7.** Liquid Clustering frente a particionado + Z-ORDER:
- A) Es lo mismo con otro nombre
- B) Layout flexible por `CLUSTER BY` sin sobre/sub-particionar; no requiere reescritura ZORDER
- C) Solo para tablas pequeñas
- D) Incompatible con Unity Catalog

**P8.** Para diagnosticar por qué una tabla es lenta al filtrar, lo primero:
- A) `DESCRIBE HISTORY` para ver archivos/operaciones
- B) Borrar la tabla
- C) Aumentar el cluster sin mirar nada
- D) Convertir a CSV

---

## Soluciones
1. **B** — Cada commit es un JSON atómico en `_delta_log`.
2. **B** — RESTORE crea una nueva versión que revierte el estado.
3. **B** — VACUUM borra ficheros físicos fuera de retención; por eso el time travel muere.
4. **B** — Idempotencia nativa: relanzar COPY INTO no duplica.
5. **B** — Patrón upsert/SCD estándar.
6. **B** — Z-ORDER maximiza data skipping en los archivos compactados.
7. **B** — Liquid Clustering es la evolución recomendada sobre partition+ZORDER.
8. **A** — HISTORY muestra operaciones, nº de archivos, particiones: primer paso del troubleshooting.
