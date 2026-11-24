# Preguntas de práctica — gov-06 / Monitorización de gobernanza en producción

**P1.** Las métricas de gobernanza en producción vigilan principalmente:
- A) El uptime de los clusters
- B) Calidad de datos, uso/adopción, cumplimiento de políticas y salud del programa de gobernanza
- C) Las bajas del personal
- D) El precio de la nube

**P2.** Freshness de una tabla crítica mide:
- A) Su tamaño
- B) El tiempo desde su última actualización vs el SLA esperado (¿llegan los datos a tiempo?)
- C) La fecha de creación
- D) El nº de columnas

**P3.** Un sistema de alertas de calidad bien calibrado:
- A) Alerta de todo
- B) Alerta pocas veces y siempre accionable (alert fatigue = alertas ignoradas = sistema inútil)
- C) Solo envía emails semanales
- D) Alerta solo a dirección

**P4.** Las system tables (`system.lakeflow`, `system.access`) sirven para:
- A) Nada en gobernanza
- B) Auditar jobs fallidos, accesos a tablas sensibles y patrones de uso con SQL, sin montar infraestructura extra
- C) Guardar logs de aplicaciones
- D) Facturación solamente

**P5.** Adopción de la gobernanza se mide con:
- A) Encuestas de satisfacción solamente
- B) % de tablas nuevas creadas CON owner/comentarios/expectativas sin que nadie lo persiga (cumplimiento espontáneo)
- C) Asistencia a formaciones
- D) Nº de stewards nombrados

**P6.** Un "data incident" de calidad tiene ciclo de vida:
- A) Se arregla y se olvida
- B) Detección → triaje/severidad → asignación a steward → resolución → postmortem con causa raíz
- C) Se escala siempre a dirección
- D) Se documenta solo si es grave

**P7.** Revisar quién accede a tablas con datos sensibles (auditoría) se hace con:
- A) Preguntar a los equipos
- B) `system.access.audit` filtrando por la tabla: quién, cuándo, qué acción
- C) No se puede saber
- D) Logs del firewall

**P8.** La señal de que un programa de gobernanza está maduro:
- A) Muchas políticas
- B) Incidentes detectados por el sistema antes de que los reporten los usuarios, con tiempos de resolución decrecientes
- C) Cero incidentes reportados
- D) Herramientas caras

---

## Soluciones
1. **B** — Calidad, adopción, cumplimiento y salud del programa.
2. **B** — Freshness vs SLA: la métrica de puntualidad del dato.
3. **B** — Alertas accionables o muerte por ruido.
4. **B** — Auditoría con SQL sobre system tables: sin infra extra.
5. **B** — Cumplimiento espontáneo = adopción real.
6. **B** — Ciclo completo con postmortem: así no se repite.
7. **B** — Audit logs en system tables.
8. **B** — Detección proactiva y resolución cada vez más rápida.
