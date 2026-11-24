# Preguntas de práctica — wr-04 / APIs y web scraping

**P1.** Ante un HTTP 429 (rate limit) la respuesta correcta es:
- A) Reintentar inmediatamente en bucle cerrado
- B) Backoff exponencial con esperas crecientes y respeto de `Retry-After`
- C) Cambiar la IP y seguir igual
- D) Pasar a scraping

**P2.** La API key de FRED en un notebook de Databricks se lee con:
- A) `open("key.txt")`
- B) `dbutils.secrets.get(scope, key)` tras guardarla en un secret scope
- C) Hardcodeada en una variable
- D) En el README del repo

**P3.** Antes de scrapear una web hay que revisar:
- A) El color del logo
- B) `robots.txt` y los ToS: si prohíben scraping, no se hace
- C) El número de visitas
- D) El framework del frontend

**P4.** La paginación típica de una API REST se implementa:
- A) Pidiendo todo de golpe
- B) Bucle sobre `page`/`offset` (o cursor) hasta que la respuesta viene vacía, guardando cada página
- C) Solo la primera página: el resto se infiere
- D) Con GraphQL

**P5.** OAuth2 client-credentials en dos frases:
- A) Usuario y contraseña en cada request
- B) Intercambio client_id+secret por un access token en el token endpoint; el token firma las llamadas posteriores
- C) Cifrado de la API key
- D) Un tipo de cookie

**P6.** ¿Por qué `raise_for_status()` después de cada `requests.get`?
- A) Acelera la descarga
- B) Convierte errores HTTP (4xx/5xx) en excepciones detectables en vez de datos basura silenciosos
- C) Es requerido por JSON
- D) Cierra la conexión

**P7.** En Free Edition, si una API no responde desde el notebook:
- A) Es culpa de la API
- B) La salida a Internet está restringida a dominios de confianza: descarga en local y sube al Volume
- C) Hay que pagar
- D) Usar `%sh wget` lo arregla siempre

**P8.** Scraping ético mínimo:
- A) Identificarse con User-Agent honesto, ritmo bajo, solo datos públicos y permitidos, cachear respuestas
- B) Rotar user-agents para parecer humano
- C) Scrapear de noche
- D) Nunca cachear

---

## Soluciones
1. **B** — Backoff + Retry-After: ser buen cliente de API.
2. **B** — Secrets: la regla de oro del repo.
3. **B** — Legalidad primero: robots.txt + ToS.
4. **B** — Paginación explícita hasta agotar resultados.
5. **B** — Token endpoint → bearer token → llamadas.
6. **B** — Fallar rápido y visible, no parsear HTML de error como si fuera JSON.
7. **B** — Restricción de salida de Free Edition: patrón descarga local → Volume.
8. **A** — Ética = honestidad, ritmo y permiso.
