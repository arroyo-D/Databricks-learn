# Preguntas de práctica — wr-06 / Código limpio y refactor de notebooks

**P1.** La señal nº 1 de que un notebook necesita refactor:
- A) Tiene muchas celdas
- B) Celdas que dependen de orden de ejecución manual y variables globales mutadas a lo largo de 40 celdas
- C) Usa pandas
- D) Tiene gráficos

**P2.** Extraer funciones de un notebook sirve para:
- A) Que ocupe menos
- B) Testear unidades, reutilizar entre notebooks (via `%run`/módulos) y documentar contratos
- C) Evitar usar Git
- D) Nada, es estética

**P3.** Un "code smell" típico en notebooks de datos:
- A) Funciones cortas
- B) Código duplicado con copiar-pegar entre celdas con solo el nombre de columna cambiando
- C) Comentarios
- D) Type hints

**P4.** Parametrizar rutas y fechas en vez de hardcodearlas:
- A) Es más lento
- B) Permite reejecutar el pipeline con otros datos/entornos sin tocar el código (widgets/dbutils)
- C) Solo sirve para producción
- D) Lo prohíbe Databricks

**P5.** El objetivo de un test unitario de una función de limpieza:
- A) Probar con todo el dataset
- B) Un caso pequeño con entrada conocida → salida esperada exacta (incluidos casos borde: nulos, vacío, formatos raros)
- C) Medir velocidad
- D) Que el código compile

**P6.** Al refactorizar, el paso 0 antes de tocar nada:
- A) Borrar celdas
- B) Guardar el output actual como referencia y verificar que tras el refactor produce LO MISMO
- C) Renombrar variables
- D) Añadir logs

**P7.** Un módulo Python compartido (`utils.py`) en el repo se usa desde un notebook con:
- A) Copiar-pegar
- B) Git folders + import del módulo (o `%run` para notebooks utilitarios)
- C) No se puede
- D) Solo con JARs

**P8.** Comentarios buenos vs malos:
- A) Comentar cada línea
- B) Comentar el PORQUÉ de decisiones no obvias; el QUÉ lo dice el código con buenos nombres
- C) No comentar nunca
- D) Comentar solo los errores

---

## Soluciones
1. **B** — Dependencia de orden de ejecución = notebook frágil e irreproducible.
2. **B** — Testeo, reutilización y contratos claros.
3. **B** — Copiar-pegar con variaciones mínimas pide una función parametrizada.
4. **B** — Parametrización = mismo código, distintos entornos.
5. **B** — Casos pequeños deterministas, con casos borde.
6. **B** — Refactor seguro = comportamiento idéntico verificable.
7. **B** — Imports reales vía Git folders.
8. **B** — El porqué en comentarios; el qué en nombres.
