# cap-01 · Plataforma de analítica de restaurantes end-to-end (streaming + batch + IA)

> **Proyecto integrador** — une en una sola plataforma todo lo de `dbx-01` a `dbx-10` más IA aplicada.
> Basado en el proyecto del vídeo *Databricks End-To-End Project | Zero-To-Expert* (restaurantes: streaming Event Hub + batch Azure SQL + medallion + SDP + sentimiento con IA + AI/BI) y reforzado por *Full End-to-End Data Engineering Project in Databricks*.

**Nivel:** 🔴 Proyecto cumbre de ingeniería · **Duración estimada:** 3–4 semanas (2–3 h/día)
**Integra:** `dbx-01`, `dbx-02`, `dbx-03`, `dbx-05`, `dbx-06`, `dbx-07`, `dbx-08`, `dbx-09`, `dbx-10`

## El escenario

Una cadena de restaurantes quiere una plataforma de analítica operativa en tiempo casi real:

- **Streaming**: eventos de pedidos llegan continuamente (en el vídeo: Azure Event Hub; aquí: generador propio + Auto Loader o Kafka simulado).
- **Batch**: datos maestros (restaurantes, menús, empleados, inventario) en una base relacional (en el vídeo: Azure SQL; aquí: cualquier SQL accesible, o Delta como origen simulado con CDC).
- **IA**: las reseñas de clientes se analizan con IA (sentimiento, temas) como parte del pipeline.
- **Consumo**: dashboards AI/BI + Genie para preguntas en lenguaje natural.

## Arquitectura objetivo

```
Generador pedidos ──► Event Hub / Auto Loader ──► bronze_orders (stream)
Datos maestros SQL ──► Lakeflow Connect (CDC) ──► bronze_dim_*
Reseñas ──► Volume ──► bronze_reviews
        │
        ▼  Lakeflow Spark Declarative Pipelines
silver: pedidos limpios + dims SCD + reseñas con sentimiento (ai_query)
        │
        ▼
gold: ventas por restaurante/hora, KPIs operativos, sentimiento por local/plato
        │
        ├─► AI/BI Dashboards (operaciones + sentimiento)
        ├─► Genie space (preguntas NL sobre gold)
        └─► Lakeflow Job orquestando todo
```

## Fases

### Fase 1 — Fundación y datos (semana 1)

- [ ] Catálogo `cap_restaurantes` con esquemas bronze/silver/gold (aplica `dbx-01`).
- [ ] Generador sintético de pedidos (notebook que escribe JSON continuamente a un Volume; ~1 evento/seg con picos).
- [ ] Datos maestros: crea tablas relacional-estilo (restaurantes, menus, empleados) y simula cambios (CDC: inserts/updates periódicos).
- [ ] Corpus de 200+ reseñas sintéticas de clientes (variadas: elogios, quejas, mixtas, en varios idiomas si quieres).

### Fase 2 — Ingesta (semana 1–2)

- [ ] Streaming: Auto Loader (o Structured Streaming sobre el Volume) → `bronze_orders` con `checkpointLocation` y schema evolution (aplica `dbx-05`, `dbx-06`).
- [ ] Batch CDC: ingesta de los maestros con `APPLY CHANGES INTO` estilo CDC (aplica `dbx-09`).
- [ ] Reseñas: carga batch al bronze.

### Fase 3 — Silver con SDP (semana 2)

- [ ] Pipeline declarativo: `silver_orders` limpio (tipos, dedupe, reglas con expectations: importe > 0, timestamp válido).
- [ ] Dimensiones con SCD tipo 2 donde tenga sentido (empleados, menús).
- [ ] `silver_reviews`: sentimiento (`ai_query` o `ai_analyze_sentiment`), temas extraídos, idioma detectado.

### Fase 4 — Gold + orquestación (semana 3)

- [ ] `gold_ventas_hora`: ventas, ticket medio, nº pedidos por restaurante/hora/día-semana.
- [ ] `gold_sentimiento_local`: score de sentimiento por local y plato, tendencia semanal.
- [ ] Lakeflow Job orquestando: ingesta batch → pipeline SDP → refresco de dashboards (aplica `dbx-08`).
- [ ] Gestión de errores: quarantine de registros malos + alerta si el stream se atrasa.

### Fase 5 — Consumo e IA conversacional (semana 3–4)

- [ ] Dashboard de operaciones: ventas en vivo, comparativas, mapa de calor horario (aplica `dbx-10`).
- [ ] Dashboard de voz del cliente: sentimiento por local, temas negativos recurrentes, reseñas destacadas.
- [ ] Genie space sobre gold: "¿qué restaurante vendió más ayer entre 13h y 15h?".

### Fase 6 — Pulido y portfolio

- [ ] Diagrama de arquitectura final + README como doc interna.
- [ ] Demo: lanza el generador, observa el flujo bronze→gold en vivo y pregúntale a Genie.

## Referencias del vídeo (opcional, como apoyo visual)

- Vídeo 1: https://www.youtube.com/watch?v=vy4G86q1rQY — capítulos: datos sintéticos (5:51), Event Hub (9:45), Azure SQL (19:03), Unity Catalog (27:52), Lakeflow Connect CDC (41:40), SDP (1:04:27), streaming (1:24:29), Silver (1:42:44), Gold (2:26:32), Workflows (3:20:38), AI/BI + sentimiento (3:31:19).
- Vídeo 2 (refuerzo): https://www.youtube.com/watch?v=SSz7HiyQN1M
- Docs: https://docs.databricks.com/

## Criterios de éxito

- [ ] Un pedido generado aparece en gold en <2 min sin intervención.
- [ ] Un UPDATE en un maestro se propaga vía CDC y cambia el gold correspondiente.
- [ ] El sentimiento de una reseña negativa es visible en el dashboard.
- [ ] Genie responde 5 preguntas de negocio sin tocar SQL.

## ⚠️ Seguridad

- Datos 100% sintéticos. Si conectas un SQL real, credenciales en Databricks Secrets.
