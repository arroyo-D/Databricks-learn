# Preguntas de práctica — wr-08 / Visualización de datos

**P1.** Para mostrar la evolución de ventas en 24 meses:
- A) Pie chart
- B) Línea temporal (el tiempo en el eje X es lo natural)
- C) Histograma
- D) Treemap

**P2.** El problema de un eje Y truncado (que no empieza en 0) en un gráfico de barras:
- A) Ninguno
- B) Exagera visualmente diferencias pequeñas: engaña al lector aunque los números sean reales
- C) Es obligatorio
- D) Solo afecta a líneas

**P3.** Un dashboard AI/BI efectivo tiene:
- A) Todos los gráficos posibles
- B) Una jerarquía: KPI principal arriba → gráfico clave → detalle; cada visual responde una pregunta
- C) Muchos colores
- D) Tablas sin formato

**P4.** Para la relación entre dos variables numéricas con 10.000 puntos:
- A) Barras
- B) Scatter con transparencia (alpha) para ver densidad y patrones
- C) Pie
- D) Tabla completa

**P5.** La elección de color correcta:
- A) Arcoíris para todo
- B) Paleta secuencial para magnitudes, divergente para desviaciones respecto a un punto medio, categórica para grupos; pensando en daltonismo
- C) Rojo/verde siempre
- D) Da igual

**P6.** Un gráfico "honesto" incluye:
- A) Solo lo que favorece la conclusión
- B) Título que afirma el hallazgo, ejes etiquetados con unidades, fuente de datos y limitaciones visibles
- C) 3D y sombras
- D) Animaciones

**P7.** En Databricks, una visualización de notebook se comparte con negocio mejor como:
- A) Captura de pantalla por email
- B) Dashboard AI/BI publicado con datasets gobernados en UC y refresco programado
- C) Exportando el notebook
- D) Imprimiéndolo

**P8.** Antes de publicar un gráfico, la pregunta de control es:
- A) "¿Es bonito?"
- B) "¿Alguien que no hizo el análisis entiende qué dice en 10 segundos y qué decisión toma con él?"
- C) "¿Tiene muchos datos?"
- D) "¿Usa mi color favorito?"

---

## Soluciones
1. **B** — Series temporales = línea.
2. **B** — Truncar ejes en barras distorsiona proporciones.
3. **B** — Jerarquía visual al servicio de decisiones.
4. **B** — Scatter + alpha para densidad en n grande.
5. **B** — Tipo de dato determina paleta; accesibilidad siempre.
6. **B** — Honestidad visual: título-hallazgo, unidades, fuente, límites.
7. **B** — AI/BI gobernado > capturas.
8. **B** — El test de los 10 segundos y la decisión.
