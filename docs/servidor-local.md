# Tu servidor (32 GB RAM / 28 núcleos) — setup para el programa

El servidor cubre lo que Free Edition no puede: Kafka, un origen relacional real, LLM local y datos privados. Todo con Docker, sin instalar nada en el host. Presupuesto de recursos: ~6 GB RAM en total, sobrado.

> ⚠️ **Seguridad primero**: este servidor vive en tu red. No expongas ningún puerto a Internet sin TLS + auth. Todo lo de abajo escucha en localhost/LAN. Las contraseñas de ejemplo (`cambia-esto`) son placeholders: pon las tuyas en un `.env` que NO se sube a Git.

## 1. Docker Compose único

Crea `~/stack-datos/docker-compose.yml` en el servidor:

```yaml
services:
  kafka:
    image: apache/kafka:3.9.0
    container_name: kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka:9093
      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://IP_DE_TU_SERVIDOR:9092
      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      CLUSTER_ID: MkU3OEVBNTcwNTJENDM2Qk
    volumes:
      - kafka_data:/var/lib/kafka/data

  postgres:
    image: postgres:16
    container_name: postgres
    ports:
      - "5432:5432"
    environment:
      POSTGRES_PASSWORD: ${PG_PASSWORD}     # en .env, no en Git
    volumes:
      - pg_data:/var/lib/postgresql/data

  ollama:
    image: ollama/ollama:latest
    container_name: ollama
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama

volumes:
  kafka_data:
  pg_data:
  ollama_data:
```

`.env` (mismo directorio, en tu `.gitignore` mental: nunca al repo):

```
PG_PASSWORD=cambia-esto-por-algo-largo
```

Arranque: `docker compose up -d`. Verificación: `docker compose ps` → los 3 en `running`.

## 2. Kafka — para cap-06 (Smart City)

- Crear los 5 topics del proyecto:
  ```bash
  docker exec kafka /opt/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 \
    --create --topic vehicles --partitions 3
  # repite con: gps, traffic, weather, emergencies
  ```
- Los generadores Python de cap-06 corren en el propio servidor (`pip install kafka-python`) y publican en `IP_DE_TU_SERVIDOR:9092`.
- **Conectividad con Databricks Free Edition**: la salida a Internet de serverless está restringida a dominios de confianza, así que `readStream.format("kafka")` contra tu IP probablemente **no conecte**. cap-06 lo resuelve con dos rutas:
  1. **File-drop** (recomendada): un consumidor en el servidor vuelca micro-batches a JSON y los sube al Volume con la CLI/SDK de Databricks → Auto Loader los ingiere. Mismo aprendizaje de streaming, cero problemas de red.
  2. **100% local**: PySpark open source en el servidor leyendo de Kafka y escribiendo Delta local. La API es idéntica a la de Databricks.

## 3. Postgres — origen relacional para cap-01 y an-04

- **cap-01**: los datos maestros (restaurantes, menús, empleados) viven aquí como "la base de datos del negocio". Practicas cambios (inserts/updates) y la ingesta CDC. Como Free Edition no puede conectar saliente a tu Postgres, exportas periódicamente a CSV/Parquet (`COPY ... TO`) y subes al Volume — o modelas el maestro directamente en Delta. El módulo contempla ambas.
- **an-04 (variante privada)**: tu warehouse personal con exports reales (Spotify, banco) vive AQUÍ, nunca en Free Edition (sus términos permiten entrenar modelos con tu contenido). Mismo esquema en estrella, misma app Streamlit, pero local:
  ```bash
  pip install duckdb streamlit pandas
  streamlit run app.py   # en el servidor, accedes por http://IP:8501
  ```

## 4. Ollama — LLM local gratuito (fallback del track ai)

- Descargar un modelo que quepa en RAM: `docker exec ollama ollama pull llama3.1:8b` (≈5 GB) o `qwen2.5:7b`.
- API compatible OpenAI en `http://IP_DE_TU_SERVIDOR:11434/v1` — desde tu Mac:
  ```python
  from openai import OpenAI
  client = OpenAI(base_url="http://IP_DE_TU_SERVIDOR:11434/v1", api_key="ollama")
  ```
- **Cuándo usarlo**: si un Foundation Model concreto no está disponible en Free Edition, o para iterar prompts del track ai sin gastar cuota diaria. La versión final de cada módulo se hace con `ai_query` en Databricks (es lo que aprendes a gobernar), pero el desarrollo iterativo puede ir contra Ollama.
- Nota: desde notebooks de Free Edition no podrás llamar a tu IP (restricción de salida); Ollama se usa desde tu Mac o en scripts del propio servidor.

## 5. Entrenamiento CPU — ml-01

El LSTM de anomalías y el baseline de exoplanetas entrenan en CPU perfectamente. Dos opciones: serverless de Databricks (cuidando la cuota diaria) o tu servidor (28 núcleos, sin cuota). Si entrenas en el servidor, apunta MLflow al workspace:

```python
import mlflow
mlflow.set_tracking_uri("databricks")   # con perfil configurado: databricks configure --profile TU_WORKSPACE
mlflow.set_experiment("/Users/tu@email/ml-01-nasa")
```

Así los experimentos quedan centralizados en Databricks aunque el cómputo sea local.

## 6. Mantenimiento

- `docker compose logs -f kafka` / `pull` mensual de imágenes.
- Los volúmenes Docker persisten aunque reinicies.
- Si el servidor se apaga: `docker compose up -d` y todo vuelve (Kafka y Postgres son autoconsistentes).
