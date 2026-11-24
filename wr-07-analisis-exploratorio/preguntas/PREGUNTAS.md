# Preguntas de práctica — wr-07 / Análisis exploratorio (EDA)

**P1.** El primer paso de cualquier EDA:
- A) Hacer un pairplot
- B) `shape`, `dtypes`, `describe()`, conteo de nulos y duplicados: la radiografía mínima
- C) Entrenar un modelo
- D) Borrar outliers

**P2.** Una correlación de 0,9 entre dos variables implica:
- A) Causalidad directa
- B) Asociación lineal fuerte; puede deberse a una tercera variable o al azar — nunca implica causalidad por sí sola
- C) Error en los datos
- D) Que una es el cuadrado de la otra

**P3.** Para comparar la distribución de precios entre 5 ciudades, el gráfico adecuado:
- A) Pie chart
- B) Boxplots por ciudad (mediana, cuartiles y outliers de un vistazo)
- C) Línea temporal
- D) Scatter

**P4.** Una distribución con media ≫ mediana indica:
- A) Datos corruptos
- B) Asimetría a la derecha (cola larga de valores altos): reporta mediana además de media
- C) Normalidad perfecta
- D) Nada relevante

**P5.** En un join exploratorio, lo que hay que vigilar SIEMPRE:
- A) El orden de las columnas
- B) El cambio en el número de filas (fan-out por duplicados en la clave) y los no-emparejados
- C) Los índices
- D) El tipo de join solo

**P6.** El EDA se documenta:
- A) En la memoria
- B) Celdas markdown con los hallazgos junto al código que los produce: preguntas → evidencia → conclusión
- C) En un email
- D) Solo en el gráfico final

**P7.** Antes de plotear una columna con 3 valores extremos entre 50.000 normales:
- A) Borrar las filas
- B) Investigarlas (¿error o real?) y considerar escala log / vista recortada declarándolo
- C) Ignorarlas
- D) Cambiarlas por la media

**P8.** Una pregunta EDA bien formulada es:
- A) "¿Qué puedo encontrar en estos datos?"
- B) "¿Los pedidos de fin de semana tienen ticket medio mayor que los de entre semana?" (falsable y medible)
- C) "¿Son buenos los datos?"
- D) "¿Cuántas filas hay?"

---

## Soluciones
1. **B** — Radiografía antes que diagnóstico.
2. **B** — Correlación ≠ causalidad: lo repetirás toda tu carrera.
3. **B** — Boxplot = comparación de distribuciones por grupo.
4. **B** — Asimetría derecha: la media engaña, la mediana informa.
5. **B** — El fan-out silencioso es el error más caro del análisis.
6. **B** — Narrativa + código: el notebook ES el informe exploratorio.
7. **B** — Investigar y declarar, nunca ocultar ni corregir a ciegas.
8. **B** — Preguntas falsables: así se convierte EDA en análisis.
