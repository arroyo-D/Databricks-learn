# AN-02 — Simulación de A/B test + análisis de cohortes

> Proyecto 5 del *7 Data Analyst Projects Roadmap*: experimentos y retención, trasladado a Spark SQL y Delta. Es el módulo más "product analytics" del repo.

## Objetivo

Generar tú mismo un dataset de usuarios **con un efecto conocido plantado**, y demostrar que eres capaz de detectarlo con rigor estadístico (y de detectar cuándo un experimento está roto). Después, construir el triángulo de retención por cohortes en SQL puro.

## Por qué importa

Los A/B tests son el pan de cada día de un analista de producto, y donde más se miente sin querer: tests sin potencia suficiente, picos de una hora que se toman por efecto real, asignaciones 52/48 que invalidan todo. Como aquí **tú plantas el efecto**, sabes la respuesta correcta — si tu análisis no la encuentra, el fallo está en tu método, no en los datos. El triángulo de cohortes, además, es la consulta SQL por antonomasia en entrevistas de producto.

## Datos

- Dataset **sintético generado por ti** (parte del ejercicio es el simulador).
- Calculadoras de referencia (verificadas): https://www.evanmiller.org/ab-testing ✅
- statsmodels: https://www.statsmodels.org/stable/index.html ✅

## Prerrequisitos

- `dbx-03` (transformaciones) y `wr-07` (EDA).

## Tareas

### Parte A — El simulador

- [ ] Genera 50.000 usuarios sintéticos: `user_id, fecha_registro` (repartida en 12 semanas), `pais, dispositivo, canal`.
- [ ] Asigna cada usuario a `variante` A o B **aleatoriamente al 50%** (usa `rand()` con semilla fija para reproducibilidad).
- [ ] Genera la conversión: base 12% en A. **Planta** un efecto de +2 puntos porcentuales en B (14%). Genera `convirtio` con una Bernoulli por usuario.
- [ ] Genera actividad: para cada usuario, eventos de actividad en los 28 días siguientes a su registro (los que convierten, más activos). Tabla Delta `ab.eventos` + tabla `ab.usuarios`.

### Parte B — El análisis del experimento

- [ ] **SRM check** (Sample Ratio Mismatch): ¿el split es realmente ~50/50? Chi-cuadrado sobre los conteos. Si falla, el experimento no es válido — dilo.
- [ ] Tasa de conversión por variante con IC 95% (wilson o normal).
- [ ] **z-test de dos proporciones** (`statsmodels.stats.proportion.proportions_ztest`). ¿Detecta tus +2 pp? ¿p-valor?
- [ ] Compara tu resultado con la calculadora de https://www.evanmiller.org/ab-testing (sección *Significance*): mismos números de entrada deben dar el mismo veredicto.
- [ ] **Potencia**: con https://www.evanmiller.org/ab-testing/sample-size.html, calcula el n mínimo para detectar +2 pp con 12% base (α=0,05, potencia 80%). ¿Tus 50.000 sobran o justan?
- [ ] Repite el test **por día** durante 14 días y grafica el p-valor diario: observa cómo "significativo" entra y sale hasta estabilizarse. Escribe 3 líneas sobre por qué no se puede parar un test al primer p<0,05 (*peeking*).
- [ ] Análisis de guardarraíles: varianza de la conversión por variante; segmenta por `dispositivo` y comprueba que el efecto no es negativo en ninguno.

### Parte C — Triángulo de retención (SQL puro)

- [ ] Cohorte = semana de `fecha_registro`. Para cada cohorte y cada `semana_desde_registro` (0–4): % de usuarios de la cohorte con ≥1 evento esa semana.
- [ ] Escríbelo como UNA query Spark SQL sobre `ab.usuarios` + `ab.eventos` (pista: `date_trunc('week', ...)`, `datediff`, `pivot` o `group by` + suma condicional).
- [ ] Formatea el triángulo en pandas (heatmap con `matplotlib`/`seaborn`).
- [ ] Interpreta: ¿retiene mejor una cohorte concreta? ¿Hay *flattening* (la curva se aplana = núcleo fiel) o cae a cero?

### Parte D — Preguntas de entrevista (respóndelas por escrito en `respuestas.md`)

- [ ] "El test da +2 pp significativo pero la varianza se dispara en B. ¿Lanzas?" 
- [ ] "El equipo quiere parar el test en el día 3 porque p=0,03. ¿Qué respondes?"
- [ ] "¿Qué es SRM y por qué invalida un experimento?"

## Criterios de éxito

- [ ] El z-test recupera el efecto plantado (+2 pp) con p < 0,05 y el IC lo contiene.
- [ ] SRM calculado y documentado; si lo fuerzas a fallar (prueba a asignar 52/48), tu check lo detecta.
- [ ] Triángulo de cohortes en una sola query SQL, con heatmap y una interpretación escrita.
- [ ] Las 3 preguntas de entrevista respondidas con argumentos, no con "depende".

## Recursos

- Calculadoras A/B: https://www.evanmiller.org/ab-testing ✅
- `proportions_ztest`: https://www.statsmodels.org/stable/index.html ✅

## ⚠️ Seguridad

Dataset 100% sintético: no uses listas de usuarios reales ni siquiera "para probar". Los `user_id` son secuencias, sin emails ni nombres.

## Entregable

Notebooks `simulador_ab`, `analisis_ab`, `cohortes` + `respuestas.md` + heatmap del triángulo en la bitácora.
