# Preguntas de práctica — wr-01 / Python fundamentos para datos

**P1.** ¿Por qué se prefieren las listas por comprensión a los bucles `for` con `append` en notebooks de datos?
- A) Son la única forma en Spark
- B) Más legibles y normalmente más rápidas para transformaciones 1:1
- C) Usan menos disco
- D) Son obligatorias en Python 3.12+

**P2.** La diferencia clave entre una lista y un generador al procesar un CSV de 10 GB:
- A) El generador produce los elementos perezosamente (uno a uno) sin cargar todo en memoria
- B) El generador es más lento pero más seguro
- C) La lista no permite índices
- D) No hay diferencia

**P3.** `df["col"].apply(mi_funcion)` vs una UDF de Spark sobre un DataFrame de Spark:
- A) Son lo mismo
- B) `apply` de pandas actúa sobre datos ya en el driver; una UDF de Spark se ejecuta distribuida en los executors
- C) La UDF siempre es más rápida
- D) `apply` no existe en pandas

**P4.** Un diccionario `{pais: [fechas]}` se construye mejor con:
- A) `dict.setdefault()` o `collections.defaultdict(list)`
- B) 3 bucles anidados
- C) Una lista de listas
- D) JSON puro

**P5.** ¿Qué imprime `type(pd.read_csv("x.csv").dtypes["fecha"])` si la columna tiene fechas como texto?
- A) `datetime64`
- B) `object` (string) — las fechas hay que parsearlas con `pd.to_datetime`
- C) `date`
- D) Error

**P6.** La forma correcta de manejar una excepción al parsear una fila:
- A) `try/except` alrededor de TODO el bucle
- B) `try/except` por fila registrando el error y continuando (fail-safe por registro)
- C) Ignorar los errores
- D) `sys.exit()`

**P7.** ¿Por qué funciones puras (sin estado global) en notebooks?
- A) Son más bonitas
- B) Testeables, reutilizables y paralelizables sin efectos colaterales
- C) Ocupan menos memoria
- D) Lo exige Git

**P8.** `f"{precio:.2f} €"` es:
- A) Una expresión regular
- B) Un f-string con formato a 2 decimales
- C) Un método de pandas
- D) Sintaxis de SQL

---

## Soluciones
1. **B** — Comprensión = intención clara y mejor rendimiento en transformaciones simples.
2. **A** — Lazy evaluation: clave para ficheros grandes.
3. **B** — pandas = memoria del driver; Spark UDF = distribuido (con coste de serialización).
4. **A** — Patrón estándar de agrupación en Python.
5. **B** — pandas no adivina fechas: parseo explícito.
6. **B** — Granularidad por fila: un dato malo no tira el pipeline.
7. **B** — La reusabilidad en pipelines depende de no depender de estado oculto.
8. **B** — Formato numérico en f-strings.
