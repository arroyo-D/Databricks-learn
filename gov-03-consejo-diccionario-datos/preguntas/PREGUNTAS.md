# Preguntas de práctica — gov-03 / Consejo de gobernanza y diccionario de datos

**P1.** El consejo de gobernanza de datos existe para:
- A) Aprobar presupuestos de TI
- B) Tomar decisiones transversales: políticas, estándares, disputas entre dominios, prioridades del programa
- C) Revisar código
- D) Sustituir al CDO

**P2.** La cadencia sana de un consejo es:
- A) Diaria
- B) Periódica (p. ej. mensual) con agenda, actas y decisiones registradas — no reuniones "a ver qué sale"
- C) Solo cuando hay crisis
- D) Anual

**P3.** Una entrada de diccionario de datos mínima incluye:
- A) Solo el nombre del campo
- B) Nombre, definición de negocio, tipo/formato, dominio de valores, steward responsable y ejemplos
- C) El DDL completo
- D) El precio del dato

**P4.** Diccionario de negocio vs catálogo técnico:
- A) Son lo mismo
- B) El diccionario define la SEMÁNTICA acordada (qué significa "cliente activo"); el catálogo inventaria los assets físicos (tablas, columnas, lineage)
- C) El técnico es para negocio
- D) El diccionario lo genera Spark

**P5.** En Databricks, las definiciones se materializan con:
- A) Notas adhesivas
- B) `COMMENT ON TABLE/COLUMN`, tags de UC y ownership — visibles en Catalog Explorer y usadas por Genie
- C) Un Excel en SharePoint
- D) Nada, es redundante

**P6.** El proceso para aprobar una nueva definición de negocio:
- A) La escribe quien la necesita y listo
- B) Propuesta del steward → revisión de dominios afectados → aprobación del consejo → publicación y versionado
- C) Votación en Slack
- D) La impone el área más grande

**P7.** Una definición de diccionario de calidad se reconoce porque:
- A) Es larga
- B) Es falsable: permite decidir sin ambigüedad si un registro cumple o no cumple ("activo = login en últimos 90 días")
- C) Usa jerga legal
- D) La escribió un directivo

**P8.** El mayor riesgo de un diccionario de datos:
- A) Que sea público
- B) Que quede desactualizado y nadie confíe en él (diccionario zombie) — necesita dueño y proceso de revisión
- C) Que tenga demasiadas entradas
- D) Que use Unity Catalog

---

## Soluciones
1. **B** — Decisiones transversales con autoridad delegada.
2. **B** — Ritual con memoria: agenda, actas, decisiones.
3. **B** — Semántica + responsable + ejemplos: lo mínimo útil.
4. **B** — Significado vs inventario físico: complementarios.
5. **B** — COMMENTs y tags UC: documentación viva junto al dato.
6. **B** — Flujo formal con versionado: así no degenera.
7. **B** — Falsabilidad = la prueba de una buena definición.
8. **B** — El diccionario zombie destruye la confianza en todo el programa.
