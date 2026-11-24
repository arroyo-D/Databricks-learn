# Preguntas de práctica — gov-05 / Desarrollo gobernado (DataOps)

**P1.** "Gobernanza embebida" en el desarrollo significa:
- A) Un comité que revisa cada notebook
- B) Que las reglas de gobernanza se aplican en el propio pipeline/CI (permisos, calidad, metadatos) sin fricción manual
- C) Documentar al final
- D) Gobernar solo producción

**P2.** El equivalente de "definition of done" para una nueva tabla Gold incluye:
- A) Que el notebook corra
- B) Owner asignado, COMMENTs, expectativas de calidad activas, permisos revisados y lineage verificado
- C) Que sea grande
- D) Que esté en producción desde ayer

**P3.** Los permisos por defecto para un nuevo esquema deben ser:
- A) Abierto a todos y ya se cerrará
- B) Cerrados por defecto (principio de mínimo privilegio) y se abren por petición justificada
- C) Solo para el admin
- D) Heredados siempre del metastore

**P4.** En CI/CD de datos (Asset Bundles + GitHub Actions), una PR debería bloquearse si:
- A) El autor es junior
- B) Las expectativas de calidad fallan en dev, falta documentación de columnas o se exponen secretos
- C) Cambia mucho código
- D) Es lunes

**P5.** Datos sintéticos en entornos de desarrollo:
- A) Una pérdida de tiempo
- B) Permiten desarrollar sin exponer datos reales (PII) en entornos menos controlados
- C) Solo para demos
- D) Ilegales

**P6.** El lineage automático de Unity Catalog aporta al desarrollo:
- A) Diagramas bonitos
- B) Análisis de impacto real: saber qué dashboards/modelos se rompen antes de cambiar una tabla
- C) Más velocidad de queries
- D) Compresión

**P7.** Un cambio destructivo de esquema (drop column) se gestiona:
- A) Ejecutándolo un viernes
- B) Con revisión de consumidores vía lineage, comunicación, ventana de deprecación y plan de rollback
- C) Sin avisar: si algo se rompe, se arregla
- D) Solo lo puede hacer el CDO

**P8.** La métrica de que la gobernanza embebida funciona:
- A) Nº de comités
- B) Que los equipos entregan cumpliendo las reglas SIN sentir que hay "un proceso de gobernanza" aparte
- C) Más reuniones
- D) Más documentos

---

## Soluciones
1. **B** — Gobernanza como código, invisible en el flujo.
2. **B** — Done = dato + metadatos + calidad + permisos + lineage.
3. **B** — Mínimo privilegio por defecto; apertura justificada.
4. **B** — Calidad, documentación y secretos: bloqueos automáticos.
5. **B** — Desarrollo seguro sin PII real.
6. **B** — Impact analysis antes del cambio: el superpoder del lineage.
7. **B** — Cambios destructivos = proceso, comunicación y rollback.
8. **B** — La mejor gobernanza es la que no se nota como fricción.
