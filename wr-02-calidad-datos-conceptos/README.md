# WR-02 — Calidad de Datos: Conceptos y Evaluación

> Capítulos 3 y 6 de McGregor, aplicados con PySpark sobre una tabla Delta.

## Objetivo
Aprender a evaluar la calidad de un dataset de forma sistemática: integridad y adecuación (fit).


## Por qué importa

La calidad de datos es el tema central del libro de McGregor y la razón por la que existen las capas Silver y las expectations: los datos reales llegan sucios, incompletos y duplicados. Este módulo te da el vocabulario (validez, completitud, consistencia, unicidad, actualidad) que luego aplicarás con DQX y SDP. Sin él, "limpiar datos" es quitar nulos al azar; con él, es ingeniería.

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

## Criterios de éxito

- [ ] Las 5 dimensiones de calidad explicadas con un ejemplo propio de cada una.
- [ ] Dataset inspeccionado con un informe de calidad por columna.
- [ ] Reglas de calidad priorizadas: cuáles bloquean y cuáles solo avisan.

## Recursos

- Fuentes de datos: [docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)

## Entregable
Notebook `notebooks/wr02_profiling.py` + `docs/informe_calidad.md`.