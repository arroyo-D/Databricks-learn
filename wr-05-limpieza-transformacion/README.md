# WR-05 — Limpieza y Transformación de Datos

> Capítulo 7 de McGregor en PySpark: subconjuntos, regex, fechas, deduplicación, corrección ortográfica y aumento de datos.

## Objetivo
Limpiar un dataset realista de principio a fin con PySpark.


## Por qué importa

Aquí se junta todo lo anterior: strings sucios, fechas en formatos imposibles, duplicados, outliers. El módulo traslada las recetas del libro a PySpark (`regexp_replace`, `to_date` con formatos, `approx_count_distinct`, ventanas para deduplicar) sobre un dataset real y desagradable. Es el entrenamiento directo para la capa Silver de cualquier proyecto.

## Datos
- Citi Bike (CSV reales de viajes): https://citibikenyc.com/system-data ✅
- Más fuentes: [docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)

## Tareas

### 1. Subconjuntos y split
- [ ] De un dataset de viajes, separa: viajes de fin de semana vs laborables
- [ ] Filtra por rango de fechas y por estación de origen

### 2. Regex (string matching potente)
- [ ] Valida emails con `rlike`
- [ ] Extrae el código postal de una dirección con `regexp_extract`
- [ ] Normaliza teléfonos dejando solo dígitos con `regexp_replace`

### 3. Fechas
- [ ] Parsear fechas en 3 formatos distintos con `to_date` / `to_timestamp` y `coalesce`
- [ ] Fechas tipo Excel (número de días desde 1899-12-30) → fecha real
- [ ] Extraer año/mes/día_semana y calcular duraciones con `datediff`

### 4. De-crufting y ortografía
- [ ] Quitar espacios, unificar mayúsculas (`trim`, `upper`, `initcap`)
- [ ] Corregir variantes ("Madrid"/"madrid"/"MADRID") con un mapeo o `when`
- [ ] Deduplicar con `dropDuplicates(["clave"])` conservando la fila más reciente

### 5. De ancho fijo a CSV limpio
- [ ] Convierte el fichero de ancho fijo de WR-03 en un CSV bien formado

### 6. Data augmentation
- [ ] Cruza tu tabla con un dataset externo (p. ej. tabla de provincias) para añadir columnas
- [ ] Crea 2 columnas derivadas útiles (p. ej. `es_finde`, `franja_horaria`)

### 7. Gotchas
- [ ] Documenta 3 gotchas que te hayan mordido (encoding, nulos vs vacíos, tipos implícitos...)

## Criterios de éxito

- [ ] Dataset con 5+ problemas distintos limpiado y cada regla documentada.
- [ ] Deduplicación con ventana (row_number) justificando la clave elegida.
- [ ] Tabla "quarantine" con los registros rechazados y el motivo.

## Recursos

- Fuentes de datos: [docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)

## Entregable
Notebook `notebooks/wr05_limpieza.py` + `docs/gotchas.md`.