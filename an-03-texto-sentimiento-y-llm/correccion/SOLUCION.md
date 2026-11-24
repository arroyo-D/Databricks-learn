# SOLUCIÓN — AN-03 (léela DESPUÉS de intentarlo)

## Parte A — Reviews y sentimiento

### Descarga

```python
from google_play_scraper import reviews, Sort

result, _ = reviews(
    "com.spotify.music",          # o la app que elijas
    lang="es", country="es",
    sort=Sort.NEWEST, count=3000,
)
pdf = pd.DataFrame(result)[["content", "score", "at"]]
spark.createDataFrame(pdf.rename(columns={"content": "texto"})) \
     .write.mode("overwrite").saveAsTable("texto.reviews_raw")
```

### VADER

```python
import nltk
nltk.download("vader_lexicon")
from nltk.sentiment.vader import SentimentIntensityAnalyzer
sia = SentimentIntensityAnalyzer()
# UDF pandas para escalarlo en Spark
```

### LLM con ai_query (SQL)

```sql
CREATE OR REPLACE TABLE texto.sent_llm AS
SELECT texto,
       ai_query(
         'databricks-meta-llama-3-3-70b-instruct',
         concat(
           'Clasifica el sentimiento de esta review como positivo, negativo o mixto. ',
           'Responde SOLO con JSON {"sentimiento": "...", "razon": "..."}. Review: ',
           texto)
       ) AS respuesta
FROM texto.reviews_raw;
-- luego parsea el JSON con from_json() y guarda columnas limpias
```

### Evaluación

```python
from sklearn.metrics import accuracy_score, confusion_matrix
gold = spark.table("texto.gold_labels").toPandas()
pred = spark.table("texto.sent_llm_flat").toPandas()
df = gold.merge(pred, on="texto")
print(accuracy_score(df.gold, df.llm), confusion_matrix(df.gold, df.llm))
```

**Resultado esperado:** el LLM suele ganar en reviews mixtas y con sarcasmo; VADER es instantáneo y gratis, y compite de tú a tú en reviews cortas y extremas ("fatal", "perfecta"). En inglés VADER va mejor que en español (su léxico es EN): si tus reviews son en español, probablemente el LLM gane con claridad — ese es un hallazgo para `notas_llm.md`.

**Errores comunes:**
- Evaluar "a ojo": sin gold labels, "parece que funciona" no es evaluación.
- Usar el `score` 1–5 de Google Play como única verdad: una review de 3 estrellas puede ser "mixta" y eso lo decide tu criterio de etiquetado, no el número. Documenta tu criterio.
- No guardar la respuesta cruda del LLM antes de parsearla: si el parseo falla, pierdes el dato.

### NMF de temas

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.decomposition import NMF
vec = TfidfVectorizer(max_features=5000, stop_words=STOP_ES, ngram_range=(1, 2))
X = vec.fit_transform(negativas)
nmf = NMF(n_components=6, random_state=42).fit(X)
for i, topic in enumerate(nmf.components_):
    print(i, [vec.get_feature_names_out()[j] for j in topic.argsort()[-10:]])
```

## Parte B — Tracker de empleo

```python
import requests, datetime
data = requests.get("https://www.arbeitnow.com/api/job-board-api", timeout=20).json()
hoy = datetime.date.today().isoformat()
filas = [{"url": j["url"], "titulo": j["title"], "empresa": j["company_name"],
          "descripcion": j["description"], "fecha_captura": hoy}
         for j in data["data"] if "data" in " ".join(j["tags"]).lower()]
```

Deduplicación con MERGE:

```sql
MERGE INTO empleo.ofertas t
USING nuevas s ON t.url = s.url
WHEN MATCHED THEN UPDATE SET t.ultima_vez = s.fecha_captura
WHEN NOT MATCHED THEN INSERT *;
```

Extracción: un prompt que exija JSON estricto (`{"skills": [...], "seniority": "...", "salario_min": null, "salario_max": null}`) y parseo con `from_json`. Valida: si >10% de filas no parsean, el prompt está mal, no el modelo — ajústalo y reejecuta (por eso guardas la respuesta cruda).

## Qué decir en una entrevista

> "Construí un pipeline de sentimiento comparando VADER con un LLM vía `ai_query` contra 100 etiquetas manuales —el LLM ganó en español por X puntos— y un tracker semanal de ofertas de empleo que estructura skills y seniority con LLM y los analiza en AI/BI con Genie."
