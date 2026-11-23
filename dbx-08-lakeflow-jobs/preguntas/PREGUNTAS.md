# Preguntas de práctica — dbx-08 / Dominio: Working with Lakeflow Jobs (16%)

**P1.** En un job multi-tarea, dos tareas sin dependencia entre sí:
- A) No pueden estar en el mismo job
- B) Pueden ejecutarse en paralelo
- C) Siempre fallan
- D) Requieren dos jobs separados

**P2.** La forma estándar de pasar parámetros a un notebook ejecutado como tarea de job:
- A) Variables de entorno del cluster
- B) `dbutils.widgets` + parámetros de la tarea
- C) Hardcodear valores
- D) Secrets

**P3.** Un job falla en la tarea 3 de 5. Tras corregir el error, la forma eficiente de reejecutar:
- A) Lanzar todo el job de nuevo
- B) Repair run: reejecuta solo las tareas fallidas y sus dependientes
- C) Borrar el job y recrearlo
- D) Ejecutar la tarea a mano fuera del job

**P4.** Para producción, el compute recomendado de un job es:
- A) Un all-purpose cluster compartido
- B) Un job cluster nuevo (se crea y destruye con la ejecución)
- C) El cluster de desarrollo del equipo
- D) Cualquiera, da igual

**P5.** La sintaxis cron de los schedules en Lakeflow Jobs es:
- A) Cron estándar Unix de 5 campos
- B) Quartz (incluye campo de segundos y año opcional)
- C) Solo horarios predefinidos
- D) ISO 8601

**P6.** Un job puede notificar al fallar mediante:
- A) Solo Slack
- B) Destinos de notificación (email, webhook, Teams...) configurables en el job
- C) No soporta notificaciones
- D) Solo mediante código en el notebook

---

## Soluciones
1. **B** — El DAG del job permite paralelismo entre ramas independientes.
2. **B** — Widgets = contrato estándar notebook-job.
3. **B** — Repair run reutiliza las tareas exitosas: ahorra tiempo y coste.
4. **B** — Job cluster: aislado, efímero y más barato para producción.
5. **B** — Quartz: `seg min hora día mes día_semana año?`. Ojo con los 6-7 campos.
6. **B** — Las notificaciones se configuran en el job, sin código extra.
