# Preguntas de práctica — an-01 / Tracker de precios + datos públicos

**P1.** La tabla Bronze de un tracker de precios debe escribirse siempre:
- A) Con `overwrite`
- B) Con `append` + `fecha_captura`: el histórico es la materia prima del análisis
- C) En CSV
- D) Solo la última foto

**P2.** La limpieza del precio ("Â£51.77" → 51.77) se hace en:
- A) Bronze, nada más scrapear
- B) Silver: Bronze guarda el valor crudo tal cual llegó
- C) En el scraper
- D) En el dashboard

**P3.** Un tracker de 30 días sin job programado es:
- A) Un tracker igualmente
- B) Una foto única: sin captura periódica automática no hay serie temporal
- C) Mejor, porque ahorra cómputo
- D) Imposible de hacer

**P4.** Una buena pregunta de análisis con datos públicos es:
- A) "Analizar la vivienda en España"
- B) "¿En qué provincias el precio/m² crece más rápido que el salario medio desde 2019?" (falsable, medible, accionable)
- C) "¿Cuántos datos hay?"
- D) "¿Es interesante el mercado?"

**P5.** Al cruzar dos fuentes oficiales, el enemigo nº 1 es:
- A) El formato CSV
- B) Las claves de join inconsistentes (nombres de provincia con tildes/mayúsculas distintas): normalizar y medir el % emparejado
- C) El tamaño
- D) La licencia

**P6.** En una regresión `precio ~ salario`, R² = 0,35 significa:
- A) El modelo es malo y hay que borrarlo
- B) El salario explica el 35% de la varianza del precio; el 65% restante queda en otros factores — hay que declararlo
- C) Correlación perfecta
- D) Error de cálculo

**P7.** La sección *Limitaciones* de tus conclusiones sirve para:
- A) Rellenar
- B) Declarar qué NO puedes afirmar (causalidad, granularidad, cobertura): es lo que diferencia un análisis serio
- C) Disculparse
- D) Citar fuentes

**P8.** Scraping ético y legal de precios requiere:
- A) Solo que la web cargue
- B) robots.txt + ToS que lo permitan, ritmo razonable y User-Agent honesto
- C) Proxies rotatorios
- D) Headless browser siempre

---

## Soluciones
1. **B** — Append con fecha: nunca destruir el histórico.
2. **B** — Crudo en Bronze, limpieza reproducible en Silver.
3. **B** — La automatización ES el proyecto.
4. **B** — Falsable y medible: la regla del roadmap.
5. **B** — Normalizar claves y auditar el match rate.
6. **B** — R² = varianza explicada; declara lo que queda fuera.
7. **B** — Honestidad analítica = credibilidad.
8. **B** — Permiso, ritmo y honestidad: el trío legal-ético.
