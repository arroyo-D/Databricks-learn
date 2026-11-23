# GOV-04 — Roadmap de Datos: Evalúa tu Estado Actual

> Capítulo 4 de Maffeo: assess current state, process map, future state y métricas de éxito — con datos reales de tu metastore.

## Objetivo
Hacer una auditoría del estado actual de tus datos y convertirla en un roadmap priorizado.


## Por qué importa

La gobernanza se implanta por oleadas, no de golpe: este módulo enseña a evaluar la madurez actual, priorizar dominios (¿qué datos duelen más?) y construir un roadmap realista de 2-3 oleadas con métricas de adopción. Es la diferencia entre un programa que sobrevive al primer trimestre y uno que muere en la presentación inicial.

## Tareas

### 1. Auditoría automática
- [ ] Notebook que recorra todos los catálogos/schemas/tablas y produzca una tabla de salud:
  - ¿tiene owner? ¿comentario? ¿tags de dominio? ¿columnas documentadas? ¿cuándo se modificó por última vez?
- [ ] Guarda el resultado en `zenviro.governance.health_check`

### 2. Current State Process Map
- [ ] Diagrama (Mermaid en `docs/proceso_actual.md`) de cómo fluye un dato en Zenviro hoy: fuente → raw → bronze → silver → gold → dashboard
- [ ] Marca en rojo los puntos sin control (sin expectations, sin owner...)

### 3. Future state
- [ ] `docs/estado_deseado.md`: cómo sería el mismo flujo gobernado (qué controles en cada salto)

### 4. Métricas de éxito
- [ ] Define 4 outcome metrics (no vanity metrics): p. ej. "tiempo medio en encontrar un dato", "% tablas gold con owner y diccionario", "incidencias de calidad/mes"
- [ ] Para cada una: línea base (hoy, medida con la auditoría) y objetivo a 3 meses

### 5. Roadmap priorizado
- [ ] Tabla: iniciativa | impacto (1-5) | esfuerzo (1-5) | prioridad (impacto/esfuerzo) | trimestre
- [ ] Top 3 iniciativas justificadas

## Criterios de éxito

- [ ] Evaluación de madurez por dominio con evidencias (no opiniones).
- [ ] Roadmap de 2-3 oleadas con criterios de entrada/salida por oleada.
- [ ] 3 métricas de adopción definidas y medibles en Databricks (p. ej. % tablas con owner).

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Administration*, system tables)

## Entregable
`notebooks/gov04_auditoria.py` + `docs/` (proceso_actual, estado_deseado, metricas, roadmap).