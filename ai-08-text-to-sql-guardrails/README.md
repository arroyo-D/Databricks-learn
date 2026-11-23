# ai-08 · Text-to-SQL con guardrails y detección de alucinaciones

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 8: Text-to-SQL Interface with Guardrails* de la guía BASWE.

**Nivel:** 🟡 Intermedio · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `dbx-10-databricks-sql`, `dbx-11-gobernanza`, `ai-01`

## Qué vas a construir

Una interfaz en lenguaje natural que traduce preguntas a SQL sobre un catálogo real, las ejecuta **con guardrails que bloquean operaciones destructivas**, valida que el SQL realmente responde a la pregunta, y devuelve resultados con score de confianza. En Databricks esto conecta directamente con Genie, Unity Catalog y los SQL Warehouses.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| PostgreSQL + SQLAlchemy introspection | **Unity Catalog**: `information_schema`, comentarios de columnas, sample values |
| LLM para generar SQL | `ai_query` con few-shots + comparativa con **Genie** como baseline |
| Guardrails custom | Validación sqlparse + **permisos SELECT-only de UC** + row/column masks |
| DuckDB sandbox | SQL Warehouse serverless con statement timeout |
| Streamlit frontend | AI/BI Dashboard o Databricks App |

## Fases del proyecto

### Fase 1 — Motor de prompts consciente del esquema (días 1–3)

- [ ] Extrae el esquema de tu catálogo (`samples.nyctaxi` + un esquema propio con 4–6 tablas relacionadas): tablas, columnas con tipos, PKs/FKs, comentarios, valores de ejemplo de columnas categóricas.
- [ ] Constructor dinámico de prompt: solo las tablas relevantes (filtro por similitud pregunta↔descripciones), relaciones FK, valores de ejemplo, glosario de negocio.
- [ ] 3–5 few-shots pregunta→SQL específicos de este esquema.
- [ ] Ambigüedad explícita: si la pregunta admite 2 interpretaciones ("ingresos" = bruto o neto), devuelve petición de clarificación estructurada en vez de adivinar.

### Fase 2 — Generación y capa de seguridad (días 3–6)

- [ ] Salida estructurada del LLM: SQL + explicación en lenguaje natural + confianza + tablas/columnas usadas. Valida sintaxis con sqlparse antes de ejecutar.
- [ ] Guardrails: bloquea DDL (CREATE/ALTER/DROP), DML (INSERT/UPDATE/DELETE), fuerza LIMIT si falta, rechaza subqueries anidadas >3 niveles, estima filas escaneadas. Cada regla configurable; log de cada query bloqueada con su motivo.
- [ ] Defensa en profundidad con UC: ejecuta con un service principal con `SELECT` únicamente; aplica row filters/column masks en tablas sensibles.
- [ ] Capa de ejecución: resultados (capped), tiempo, filas devueltas, query profile. Todo a `query_log` (Delta).

### Fase 3 — Detección de alucinaciones (días 6–9)

- [ ] Back-translation: manda el SQL generado al LLM → "¿qué pregunta responde este SQL?" → compara con la original. Divergencia = flag.
- [ ] Sanity checks de resultados: agregados en rangos plausibles, fechas dentro del span de los datos, columnas NULL-heavy que sugieren JOIN malo.
- [ ] Validación multi-query: genera 2 SQL independientes (distinta estrategia de JOIN/agregación); si coinciden → confianza alta; si no → presenta ambos con explicación.
- [ ] Score compuesto: sintaxis válida + alineación back-translation + sanity checks + acuerdo multi-query + cobertura de esquema esperada.

### Fase 4 — Interfaz (días 9–11)

- [ ] Endpoint/app: pregunta NL → SQL (editable) + resultados + desglose del score de confianza + historial.
- [ ] Feedback loop: el usuario marca correcto/incorrecto; incorrectos → suite de eval; correctos → nuevos few-shots.

### Fase 5 — Suite de evaluación (días 11–13)

- [ ] Golden set de 50+ preguntas con SQL verificado y resultados esperados: lookups, JOINs multi-tabla, GROUP BY, rangos de fechas, ambiguas, sin respuesta.
- [ ] Métricas: exact match SQL, execution match (mismos resultados aunque el SQL difiera), tasa de detección de alucinaciones, efectividad de guardrails (100% queries peligrosas bloqueadas).
- [ ] Compara tu pipeline contra Genie con la misma suite (Genie es el baseline a batir).

### Fase 6 — Pulido (días 13–14)

- [ ] Demo ≤4 min: pregunta NL → SQL, guardrail bloqueando un DROP, detector pillando una mala traducción, multi-query resolviendo discrepancia.
- [ ] Narrativa liderando con seguridad: "bloquea el 100% de operaciones destructivas y detecta el Y% de alucinaciones antes de llegar al usuario".

## Criterios de éxito

- [ ] `DROP TABLE` camuflado en NL jamás se ejecuta (ni siquiera llega al warehouse).
- [ ] Execution accuracy ≥ 80% en tu golden set.
- [ ] Los errores marcados por usuarios vuelven como casos de eval.

## Datos y URLs

- Datos: `samples.nyctaxi.trips` (integrado en Databricks) + tu esquema propio con datos sintéticos.
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*Data warehousing*, *Genie*, *Data governance with Unity Catalog*).

## ⚠️ Seguridad

- El service principal de ejecución tiene SOLO `SELECT`: es tu última línea de defensa.
- No expongas tablas con datos personales al generador de SQL en un demo público.
