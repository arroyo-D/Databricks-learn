# WR-02 — Calidad de Datos: Conceptos y Evaluación

> Capítulos 3 y 6 de McGregor, aplicados con PySpark sobre una tabla Delta.

## Objetivo
Aprender a evaluar la calidad de un dataset de forma sistemática: integridad y adecuación (fit).

## Conceptos
- **Integridad**: ¿el dato es correcto y fiable? (pedigree, actualidad, completitud, consistencia...)
- **Fit (adecuación)**: ¿sirve para MI pregunta? (validez, fiabilidad, representatividad)

## Dataset
Genera o usa un CSV de ejemplo con errores típicos (nulos, duplicados, valores imposibles, formatos mezclados). Puedes usar `data/ventas_sucias.csv` de la corrección como referencia para crear el tuyo. Para datos reales: [fuentes públicas verificadas](../docs/fuentes-de-datos.md).

## Tareas

### 1. Checklist de integridad (con código)
Sobre un DataFrame de Spark:
- [ ] **Completitud**: % de nulos por columna
- [ ] **Consistencia**: misma entidad escrita de formas distintas ("Madrid", "madrid", "MADRID")
- [ ] **Validez de dominio**: edades negativas, emails sin @, importes < 0
- [ ] **Unicidad**: duplicados por clave (`groupBy(clave).count() > 1`)
- [ ] **Actualidad**: fechas futuras o muy antiguas

### 2. Data profiling
- [ ] `df.describe()` / `df.summary()` e interpretar min/max/count
- [ ] Cardinalidad por columna (nº de valores distintos)
- [ ] Tipos inferidos vs tipos esperados

### 3. Informe de calidad
- [ ] Rellena `docs/informe_calidad.md` con: para cada dimensión, qué encontraste y si el dataset es "fit" para responder "¿cuánto vendemos por ciudad al mes?"

### 4. Expectations (avanzado)
- [ ] Traduce 3 hallazgos a expectations de Lakeflow SDP (`expect_or_drop`) para que no vuelvan a colarse

## Entregable
Notebook `notebooks/wr02_profiling.py` + `docs/informe_calidad.md`.
