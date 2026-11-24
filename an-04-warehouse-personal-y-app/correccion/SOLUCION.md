# SOLUCIÓN — AN-04 (léela DESPUÉS de intentarlo)

## Parte A — Esquema en estrella

### Descarga de clima (Open-Meteo)

```python
import requests
params = {
    "latitude": 40.4168, "longitude": -3.7038,      # Madrid
    "start_date": "2015-01-01", "end_date": "2026-11-23",
    "daily": "temperature_2m_max,temperature_2m_min,precipitation_sum",
    "timezone": "Europe/Madrid",
}
data = requests.get("https://archive-api.open-meteo.com/v1/archive",
                    params=params, timeout=60).json()["daily"]
pdf = pd.DataFrame(data).rename(columns={"time": "fecha"})
```

### El esquema (lo importante)

```
dim_fecha  (fecha PK, dia, mes, nombre_mes, trimestre, anyo, estacion, es_finde)
dim_ciudad (ciudad_sk PK, nombre, lat, lon, pais)
fact_clima_diario (fecha FK, ciudad_sk FK, tmax, tmin, precipitacion)
```

**Decisiones que te preguntarán:**
- `dim_fecha` con PK la propia fecha (natural) es pragmático y está bien; surrogate keys de verdad (`20250106` int) son el estándar clásico. Cualquiera vale si lo justificas.
- `dim_fecha` se genera con `sequence()` + funciones de fecha en Spark SQL, no se descarga.
- La estación (primavera/verano...) con `CASE WHEN` sobre mes — ojo: hemisferio norte.

**Errores comunes:**
- Acabar con una tabla ancha "porque es más fácil". Si todo cabe en una tabla, no has hecho el ejercicio: el punto es practicar el modelo.
- No poner PK/FK declarativas (`ALTER TABLE ... ADD CONSTRAINT`): en Databricks documentan el modelo y ayudan al optimizador y a Genie.
- Incremental con `append` a ciegas: usa `MERGE INTO ... WHEN NOT MATCHED` por `(fecha, ciudad_sk)` para idempotencia.

## Parte B — Genie

Genie mejora con: 1) PK/FK declaradas, 2) `COMMENT ON COLUMN` en cada columna, 3) instrucciones del espacio con definiciones ("racha de calor = 3+ días consecutivos con tmax > 32°C"). El loop es: pregunta → falla → añade instrucción/sinónimo → pregunta de nuevo. Documenta 3 ciclos de este loop: eso ES el aprendizaje.

## Parte C — La app

### Esqueleto `app.py`

```python
import os, streamlit as st
from databricks import sql

st.set_page_config(page_title="Clima de mi ciudad", layout="wide")
st.title("🌡️ Clima histórico — anomalías y récords")

ciudad = st.sidebar.selectbox("Ciudad", ["Madrid", "Barcelona"])
rango = st.sidebar.date_input("Rango", ...)

@st.cache_data(ttl=3600)
def cargar(ciudad, ini, fin):
    with sql.connect(server_hostname=os.environ["DBX_HOST"],
                     http_path=os.environ["DBX_HTTP_PATH"],
                     access_token=os.environ["DBX_TOKEN"]) as c:
        return pd.read_sql(QUERY, c.connection, params=(ciudad, ini, fin))

df = cargar(ciudad, *rango)
col1, col2, col3 = st.columns(3)
col1.metric("T máx absoluta", f"{df.tmax.max():.1f} °C")
...
st.line_chart(df.set_index("fecha")[["tmax", "tmin"]])
```

### Despliegue

- **Databricks Apps**: `databricks apps create` + `app.yaml` con el comando de Streamlit; los secretos se inyectan como variables de entorno del recurso app (nunca en el código). Docs: *Databricks Apps* en docs.databricks.com.
- **HF Spaces** (alternativa): repo del Space con `app.py` + `requirements.txt`, leyendo un Parquet exportado (`df.to_parquet`) subido al propio Space. Es público: solo datos NO personales.

**Errores comunes:**
- Meter el token en `app.py` "temporalmente": así es como acaban los tokens en GitHub. `os.environ` desde el minuto uno.
- Sin `st.cache_data`: cada click reconsulta el warehouse (lento y caro).
- App sin narrativa: KPI arriba → gráfico principal → detalle abajo. Un tercero debe entenderla en 10 segundos.

## Qué decir en una entrevista

> "Monté un warehouse personal en esquema en estrella sobre Delta —clima histórico de Open-Meteo con actualización diaria incremental—, lo expuse con Genie afinando sus instrucciones, y desplegué una app Streamlit en Databricks Apps con selector de ciudad y detección de anomalías."
