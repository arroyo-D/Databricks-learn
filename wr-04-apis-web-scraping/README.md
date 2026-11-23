# WR-04 — APIs y Web Scraping desde Databricks

> Capítulo 5 de McGregor: APIs, autenticación, OAuth y scraping, con la gestión de secretos al estilo Databricks.

## Objetivo
Consumir datos web de forma correcta y segura: APIs JSON/XML, claves protegidas con Secrets, y scraping como último recurso.


## Por qué importa

Muchas fuentes empresariales no son ficheros: son APIs REST (JSONPlaceholder, FRED) o páginas web. Este módulo cubre peticiones HTTP desde Databricks, paginación, rate limiting, autenticación por API key y almacenamiento de la respuesta cruda en Bronze antes de cualquier transformación. El patrón "API → Bronze → Silver" es pan de cada día.

## Datos (URLs verificadas)
- API sin auth: https://jsonplaceholder.typicode.com/ ✅
- API con key gratuita: FRED https://fred.stlouisfed.org/docs/api/fred/ ✅ (key: https://fredaccount.stlouisfed.org/apikeys)
- Datos abiertos España: https://datos.gob.es/ ✅

## Tareas

### 1. API pública sin auth
- [ ] GET a JSONPlaceholder con `requests`
- [ ] Convertir el JSON a DataFrame y guardarlo en Delta
- [ ] Manejar errores: `raise_for_status()`, reintentos con backoff

### 2. API con clave (FRED)
- [ ] Consigue una API key gratuita de FRED
- [ ] **NUNCA** la pongas en el código: crea un secret scope y guárdala ahí
- [ ] Léela con `dbutils.secrets.get()` y haz la petición
- [ ] Paginación: descarga varias páginas de resultados con un bucle

### 3. .gitignore y credenciales
- [ ] Añade `.gitignore` con patrones de credenciales
- [ ] Verifica que ningún commit contiene la clave (revisión del historial del repo)

### 4. OAuth (teoría + esqueleto)
- [ ] Escribe el flujo OAuth2 client-credentials con `requests` (token endpoint → access token → API call)
- [ ] No necesitas cuenta real: deja el código parametrizado

### 5. Scraping (último recurso)
- [ ] Con `beautifulsoup4`, extrae una tabla HTML de una página pública que lo permita (revisa robots.txt y ToS)
- [ ] Convierte la tabla a DataFrame
- [ ] Escribe en `docs/etica_api.md`: 5 reglas de ética/ToS antes de scrapear

## ⚠️ Seguridad
Las API keys van en **Databricks Secrets**, jamás en código ni en Git. Este repo es público: revisa antes de cada push.

## Criterios de éxito

- [ ] API consumida con paginación y reintentos ante errores 429/500.
- [ ] Respuestas crudas guardadas en Bronze tal cual llegaron.
- [ ] API key en Secrets y código que la lee con `dbutils.secrets.get`.

## Recursos

- Fuentes de datos (URLs verificadas): [docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)

## ⚠️ Seguridad

Las API keys (p. ej. FRED) van en **Databricks Secrets**, nunca en el notebook ni en Git. Este repo es público: una key commiteada hay que considerarla comprometida y rotarla.

## Entregable
Notebooks en `notebooks/` + `docs/etica_api.md` + `.gitignore`.