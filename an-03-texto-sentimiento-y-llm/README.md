# AN-03 — Texto: sentimiento de reviews + tracker de empleo con LLM

> Proyectos 6 y 1 del *7 Data Analyst Projects Roadmap* unidos: los dos son pipelines de texto no estructurado → datos estructurados → análisis. En Databricks, el LLM es `ai_query` sobre Foundation Model APIs (no hace falta cuenta de Anthropic/OpenAI).

## Objetivo

1. **Reviews**: descargar reseñas de una app, etiquetar sentimiento con dos métodos (VADER y un LLM vía `ai_query`), compararlos contra una verdad de referencia etiquetada a mano, y extraer temas.
2. **Empleo**: consumir una API pública de ofertas de trabajo cada semana y usar el LLM para estructurar lo no estructurado (skills, seniority, rango salarial) y analizar tendencias.

## Por qué importa

El 80% de los datos de negocio son texto libre. Saber convertir "me encanta pero se cierra solo" en `{sentimiento: mixto, tema: estabilidad}` a escala es una habilidad top de analista — y con `ai_query` lo haces con una función SQL dentro de tu pipeline Delta, gobernado y sin mover datos fuera. La comparación VADER vs LLM contra etiquetas humanas te enseña lo que los slides no cuentan: el LLM no siempre gana, y sin etiquetas de referencia no sabes si gana.

## Datos (URLs verificadas)

- `google-play-scraper` (Python, reviews de Google Play): https://github.com/JoMingyu/google-play-scraper ✅
- API de empleo Arbeitnow (gratis, sin key): https://www.arbeitnow.com/api/job-board-api ✅
- API de empleo Remotive (remoto, gratis, sin key): https://remotive.com/api/remote-jobs ✅
- VADER (NLTK): https://www.nltk.org/howto/sentiment.html ✅
- `ai_query` (Databricks): https://docs.databricks.com/ → *ai_query* ✅

## Prerrequisitos

- `wr-04` (APIs) y `dbx-03` (transformaciones). Útil: `ai-01` (evaluación de LLMs) si ya lo hiciste.

## Tareas

### Parte A — Reviews y sentimiento

- [ ] Con `google-play-scraper` descarga 2.000–5.000 reviews de 2–3 apps que conozcas bien (necesitas conocer el dominio para etiquetar). Tabla Bronze `texto.reviews_raw` tal cual llegan.
- [ ] **Etiqueta a mano 100 reviews** (positivo/negativo/mixto) en una tabla `texto.gold_labels`. Sí, a mano. Es la hora mejor invertida del módulo: sin ella no hay evaluación, solo opiniones.
- [ ] VADER sobre todas las reviews → `texto.sent_vader`.
- [ ] LLM: `SELECT ai_query('databricks-meta-llama-3-3-70b-instruct', concat('Clasifica el sentimiento...', texto))` o desde SQL. Guarda resultado + justificación en `texto.sent_llm`.
- [ ] **Evaluación**: accuracy de VADER y del LLM contra tus 100 gold labels. Matriz de confusión de cada uno. ¿Cuál gana? ¿En qué tipo de review falla cada uno (sarcasmo, mixtas, cortas)?
- [ ] Temas: NMF (`sklearn.decomposition.NMF` sobre TF-IDF) con 5–8 temas sobre las reviews negativas. Nombra cada tema a mano mirando sus top-palabras.
- [ ] Resumen ejecutivo: "el 34% de las reviews negativas hablan de X y el sentimiento empeora desde la versión Y".

### Parte B — Tracker de empleo

- [ ] Job semanal (Lakeflow Jobs) que llame a la API de Arbeitnow y/o Remotive y guarde ofertas de data/analytics en `empleo.ofertas_raw` (append, con `fecha_captura`).
- [ ] Deduplicación: misma oferta reaparece semanas → clave `(url)` + `MERGE` para conservar primera/última aparición.
- [ ] Extracción con LLM (`ai_query` por oferta): `skills` (array), `seniority` (junior/mid/senior/lead), `salario_min/max` si aparece. Define el esquema de salida en el prompt y parsea el JSON. Tabla `empleo.ofertas_silver`.
- [ ] Análisis tras 4+ semanas: top 10 skills por demanda, % ofertas con salario publicado, seniority más demandado, evolución semanal.
- [ ] Dashboard AI/BI con las tendencias + Genie para preguntar en lenguaje natural ("¿qué skills piden las ofertas senior de esta semana?").

### Parte C — Reflexión escrita (`notas_llm.md`)

- [ ] ¿Cuándo usarías VADER y cuándo un LLM? (coste, latencia, dominio, explicabilidad)
- [ ] ¿Qué harías para evaluar la extracción de skills del LLM? (pista: mismo patrón que las 100 reviews)

## Criterios de éxito

- [ ] 100 gold labels hechas a mano y usadas para una tabla comparativa VADER vs LLM con accuracy real.
- [ ] El LLM se invoca vía `ai_query` dentro de un pipeline Delta, no con llamadas sueltas en un notebook.
- [ ] Tracker de empleo corriendo en job semanal con deduplicación por `MERGE`.
- [ ] `notas_llm.md` con decisiones justificadas, no con "el LLM es mejor".

## Recursos

- Fuentes de datos: [../docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)
- Evaluación de LLMs a fondo: [../ai-01-eval-regresiones-llm](../ai-01-eval-regresiones-llm)

## ⚠️ Seguridad

- Ambas APIs son públicas sin key. Si añades otra con key (LinkedIn, Indeed...), va a Databricks Secrets y revisa sus ToS: la mayoría prohíben scraping directo.
- Las reviews son datos públicos pero con autor: guarda texto y fecha, no perfil del autor ni datos personales.

## Entregable

Notebooks de ambas partes + tabla comparativa VADER/LLM + `notas_llm.md` + dashboard del tracker en la bitácora.
