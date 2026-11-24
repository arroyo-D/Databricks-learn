# AN-01 — Tracker de precios + análisis de datos públicos (España)

> Dos proyectos del *7 Data Analyst Projects Roadmap* (price tracker + análisis de fuentes abiertas) unidos en un solo módulo: ambos son pipelines de captura periódica → Delta → análisis, adaptados a Databricks y a fuentes españolas.

## Objetivo

1. Construir un **tracker de precios** que recolecte datos cada día durante 30+ días (pipeline real, no un notebook que se ejecuta una vez).
2. Responder una **pregunta afilada** cruzando 2–3 fuentes de datos públicos españoles con análisis estadístico.

## Por qué importa

La diferencia entre "sé pandas" y "soy analista" es demostrar que puedes hacer dos cosas: montar un sistema que **captura datos a lo largo del tiempo sin intervención** (aquí entra la ingeniería: Lakeflow Jobs, Delta, idempotencia) y hacer una **pregunta de negocio precisa** que se responde cruzando fuentes (aquí entra el análisis: joins, correlaciones, regresión). Este módulo es la prueba de las dos.

## Datos (URLs verificadas)

- Books to Scrape (web hecha para practicar scraping, sin riesgo legal): https://books.toscrape.com ✅
- Datos abiertos España: https://datos.gob.es/ ✅
- Open-Meteo Archive API (clima histórico, sin key): https://archive-api.open-meteo.com/v1/archive ✅
- statsmodels (documentación): https://www.statsmodels.org/stable/index.html ✅

## Prerrequisitos

- `dbx-03` (transformaciones) y `dbx-08` (Lakeflow Jobs) hechos.
- `wr-04` (APIs y scraping) y `wr-07` (EDA) hechos.

## Tareas

### Parte A — Tracker de precios (empieza HOY: necesita 30 días de datos)

- [ ] Lee `robots.txt` y los ToS de tu web objetivo. Para practicar usa https://books.toscrape.com (hecha para esto); para algo real, elige 2–3 tiendas que permitan scraping.
- [ ] Define la lista de 10–20 productos/URLs a vigilar (que no cambie cada día).
- [ ] Notebook `scrape_precios`: `requests` + `BeautifulSoup` → DataFrame con `producto_id, nombre, precio, disponibilidad, fecha_captura` (fecha = día de ejecución, siempre).
- [ ] Escribe con `.mode("append")` en una tabla Delta `tracker.precios_raw`. **Nunca sobreescribas**: la materia prima de un tracker es el histórico.
- [ ] Crea un **Lakeflow Job** con trigger diario (p. ej. 07:15) que ejecute ese notebook. Verifica que corre solo.
- [ ] Tabla Silver `tracker.precios`: precio limpio (strip de símbolos, cast a double), deduplicado por `(producto_id, fecha_captura)`.
- [ ] Espera 30 días reales. Mientras tanto, haz la Parte B y otros módulos — el tracker trabaja solo.

### Parte B — Pregunta afilada con datos públicos

- [ ] Formula UNA pregunta medible y accionable. Mal: "analizar el mercado laboral". Bien: *"¿En qué provincias españolas el precio medio de la vivienda crece más rápido que el salario medio desde 2019?"*
- [ ] Encuentra 2–3 fuentes en https://datos.gob.es/ (o INE) que la respondan. Descarga los CSV a un UC Volume y crea tablas Bronze.
- [ ] Cruza las fuentes (join por provincia/año o lo que toque) en una tabla Silver documentada.
- [ ] Análisis: tendencia por grupo, correlaciones y una regresión con `statsmodels` (`OLS` + `summary()`). Interpreta R² y p-valores en una celda markdown.
- [ ] Escribe en `conclusiones.md`: la pregunta, la respuesta en 3 líneas, 1 gráfico que la pruebe y una sección *Limitaciones* honesta (qué NO puedes afirmar con estos datos).

### Parte C — Dashboard

- [ ] Dashboard AI/BI con 3 gráficos: evolución de precio del producto que más ha bajado, serie temporal de tu Parte B y tabla de outliers.
- [ ] Publica el dashboard y copia el enlace en tu bitácora.

## Criterios de éxito

- [ ] El tracker tiene 30+ filas por producto (30 días) capturadas **por el job**, no a mano.
- [ ] El scraping respeta robots.txt/ToS y la Parte B usa solo fuentes oficiales.
- [ ] `conclusiones.md` responde la pregunta con un número concreto y declara sus limitaciones.
- [ ] Si borras la tabla Silver y reejecutas, se reconstruye idéntica (idempotencia).

## Recursos

- Lakeflow Jobs: https://docs.databricks.com/ → *Lakeflow Jobs* ✅
- Fuentes de datos del repo: [../docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)

## ⚠️ Free Edition

La salida a Internet desde notebooks está restringida a dominios de confianza: prueba primero un `requests.get("https://books.toscrape.com")` en una celda. Si conecta, haz el scraping directamente; si no, aplica el patrón "descarga local → Volume" de [../docs/recursos-gratuitos.md](../docs/recursos-gratuitos.md) §3 (descargas en tu Mac un snapshot de las páginas y el notebook las procesa desde el Volume; el job diario sigue teniendo sentido sobre capturas que subes tú o que generas sintéticas).

## ⚠️ Seguridad y legalidad

- Scraping solo donde esté permitido (robots.txt + ToS). Books to Scrape existe precisamente para practicar sin riesgo.
- Sin API keys en este módulo (Open-Meteo no las pide). Si añades otra fuente con key, va a Databricks Secrets.
- Este repo es público: sube código y `conclusiones.md`, **nunca** los CSV descargados si contuvieran datos personales.

## Entregable

Notebooks `scrape_precios` y `analisis_datos_publicos` + tabla `tracker.precios` con 30 días + `conclusiones.md` + captura del dashboard AI/BI en la bitácora.
