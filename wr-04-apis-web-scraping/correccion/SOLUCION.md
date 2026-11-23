# Solución — WR-04

## 1. API sin auth
```python
import requests, time

def get_json(url, retries=3):
    for i in range(retries):
        try:
            r = requests.get(url, timeout=30)
            r.raise_for_status()
            return r.json()
        except requests.RequestException:
            if i == retries - 1: raise
            time.sleep(2 ** i)  # backoff exponencial

data = get_json("https://jsonplaceholder.typicode.com/posts")
df = spark.createDataFrame(data)
df.write.format("delta").mode("overwrite").saveAsTable("main.raw.posts")
```

## 2. API key con Secrets
```bash
# una vez, con Databricks CLI:
databricks secrets create-scope apis
databricks secrets put-secret apis fred_key --string-value TU_CLAVE
```
```python
api_key = dbutils.secrets.get(scope="apis", key="fred_key")  # nunca se imprime real
url = ("https://api.stlouisfed.org/fred/series/observations"
       f"?series_id=UNRATE&api_key={api_key}&file_type=json")
obs = get_json(url)["observations"]
```

Paginación:
```python
todos, offset = [], 0
while True:
    page = get_json(f"{url}&offset={offset}&limit=100")
    if not page: break
    todos += page; offset += 100
```

## 3. .gitignore
```
.env
credentials*.json
*_key*
databrickscfg
```

## 4. OAuth2 client credentials
```python
token_resp = requests.post(token_url,
    data={"grant_type": "client_credentials"},
    auth=(client_id, client_secret)).json()
headers = {"Authorization": f"Bearer {token_resp['access_token']}"}
resp = requests.get(api_url, headers=headers)
```

## 5. Scraping
```python
from bs4 import BeautifulSoup
html = requests.get("https://example.com/tabla").text
soup = BeautifulSoup(html, "html.parser")
filas = [[c.get_text(strip=True) for c in tr.find_all(["td", "th"])]
         for tr in soup.select("table tr")]
df = spark.createDataFrame(filas[1:], filas[0])
```

Ética: (1) lee robots.txt y ToS; (2) identifícate con User-Agent; (3) limita la frecuencia; (4) no datos personales; (5) si hay API, usa la API.

## Puntos clave
- Secrets scope = la forma correcta de gestionar claves en Databricks.
- Los secretos leídos con dbutils se redactan al imprimirlos.
- Backoff exponencial y paginación: lo básico de cualquier ingesta por API.
