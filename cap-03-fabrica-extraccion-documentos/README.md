# cap-03 · Fábrica de extracción de documentos (Information Extraction)

> **Proyecto integrador IA** — une `ai-14` (procesador de documentos) y `ai-03` (forense de fallos) en un pipeline industrial.
> Basado en *State of AI Agents*: **Information Extraction** es el 2º Agent Brick más usado (**31%** del uso) — convertir documentos sin etiquetar en tablas estructuradas es una de las necesidades empresariales más comunes.

**Nivel:** 🔴 · **Duración estimada:** 3 semanas
**Integra:** `ai-14`, `ai-03` · **Prerrequisito:** `dbx-06`, `dbx-09`

## El escenario

La empresa recibe a diario documentos de proveedores en formatos caóticos: facturas PDF nativas, albaranes escaneados, contratos largos. Vas a construir la fábrica que los convierte en **tablas Delta gobernadas** con confianza por campo, revisión humana solo donde hace falta, y capacidad de diagnosticar automáticamente dónde falla el pipeline cuando falla.

## Arquitectura objetivo

```
Proveedores → Volume (dropzone) → Auto Loader
   │
   ▼
ai_parse_document / endpoint visión → texto + estructura
   ▼
clasificador de tipo → esquema Pydantic → extracción JSON estricta
   ▼
validación: tipos + reglas negocio + anomalías → confianza por campo
   ├── alta ──► gold (tablas gobernadas UC)
   └── baja ──► review queue (App) → correcciones → feedback loop
   │
   ▼ (cuando algo falla)
MLflow traces → backward analyzer (ai-03) → causa raíz + caso de eval nuevo
```

## Fases

### Semana 1 — Pipeline de extracción

- [ ] Corpus sintético: 3 tipos de documentos (facturas, albaranes, contratos) × 20 ejemplares, algunos deliberadamente difíciles (escaneados torcidos, campos ausentes, multi-moneda).
- [ ] Ingesta Auto Loader + parseo (`ai_parse_document`, fallback a visión).
- [ ] Clasificador de tipo + extracción estricta por esquema (JSON schema, "solo lo presente en el texto").
- [ ] Confianza por campo (match exacto/fuzzy, acuerdo entre chunks, formato válido).

### Semana 2 — Validación, revisión y tablas gobernadas

- [ ] Reglas de negocio: líneas suman al total, proveedor existe en maestro, fechas plausibles.
- [ ] Enrutado por confianza: auto-aprobado vs cola de revisión (App o notebook de revisión).
- [ ] Tablas gold por tipo de documento, gobernadas en UC con linaje.
- [ ] Correcciones humanas → few-shots nuevos + ajuste de umbrales.

### Semana 3 — Forense + operación

- [ ] Trazado MLflow de cada documento (spans: parseo, clasificación, extracción, validación).
- [ ] Backward analyzer: documento con salida mala → identifica el paso causante con evidencia → genera caso de eval (`ai-03`).
- [ ] Dashboard operativo: documentos/día, **tasa de auto-aprobación**, accuracy por campo, tipos de fallo más comunes, tiempo medio de revisión.
- [ ] Demo: factura escaneada → extracción → anomalía detectada → corrección → el siguiente documento similar ya sale bien.

## Criterios de éxito

- [ ] ≥80% de documentos correctos auto-aprobados sin tocar humano.
- [ ] Un fallo sistemático (p. ej. formato de fecha europeo) se diagnostica por el analyzer y se corrige una vez, no veinte.
- [ ] Linaje UC completo: de la tabla gold al archivo original en el Volume.

## Datos y URLs

- Documentos sintéticos propios (plantillas + datos ficticios). Docs: https://docs.databricks.com/

## ⚠️ Seguridad

- Nada de documentos reales con datos fiscales/personales: sintéticos únicamente.
