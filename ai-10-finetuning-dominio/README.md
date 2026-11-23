# ai-10 · Fine-tuning de un modelo en dominio específico

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 10: Fine-Tuning Pipeline with LoRA* de la guía BASWE.

**Nivel:** 🔴 Avanzado · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `ai-01`, `dbx-04-python-spark`

## Qué vas a construir

Un pipeline end-to-end que toma un dataset de dominio, aplica fine-tuning a un modelo open source sobre Databricks, evalúa el resultado contra el modelo base con un benchmark propio, y despliega el modelo ajustado en un serving endpoint — con tracking completo y reproducibilidad vía MLflow.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| Llama 3 8B + PEFT/LoRA local | **Foundation Model Training** (fine-tuning gestionado de modelos open source) o entrenamiento en cluster GPU con MLflow |
| Dataset en ficheros | Tablas Delta / Volumes (train/val/test sin leakage) |
| Weights & Biases | **MLflow** integrado (hiperparámetros, curvas, checkpoints) |
| vLLM/Ollama serving | **Model Serving** con el modelo registrado en UC |
| lm-eval-harness | MLflow Evaluate + benchmark custom en Delta |

## Fases del proyecto

### Fase 1 — Dataset de entrenamiento (días 1–4)

- [ ] Elige un dominio y tarea **estrechos y medibles**: clasificación de cláusulas legales, resumen de notas de soporte, generación de respuestas con tono de marca...
- [ ] Cura 500–2.000 ejemplos en formato instrucción (instruction, input, output). Calidad > cantidad: cada ejemplo es exactamente el comportamiento que quieres enseñar.
- [ ] Limpieza agresiva: duplicados, formato inconsistente, ejemplos incorrectos. Guarda en Delta con metadata de procedencia.
- [ ] Split 80/10/10 sin leakage (si dos ejemplos derivan del mismo documento fuente, van al mismo split). El test set es sagrado.
- [ ] Benchmark manual de 30–50 ejemplos: edge cases, ejemplos duros, modos de fallo esperados.

### Fase 2 — Pipeline de entrenamiento (días 4–7)

- [ ] Configura el fine-tuning (Foundation Model Training o cluster GPU): modelo base, epochs (1–3), learning rate, tamaño de batch. Documenta el porqué de cada valor.
- [ ] Tracking en MLflow: todos los hiperparámetros, curvas de loss, métricas de validación por checkpoint, duración y coste. Cada run reproducible desde su config.
- [ ] Early stopping / selección de checkpoint por validation loss.
- [ ] Sweep de ≥3 configuraciones (p. ej. epochs {1,3,5} × lr {1e-5, 2e-5}) → tabla comparativa en MLflow.

### Fase 3 — Evaluación base vs fine-tuned (días 7–10)

- [ ] Baseline: ejecuta el modelo base contra TODO el benchmark con el mismo prompt template. Guarda resultados (son el denominador de toda mejora).
- [ ] Ejecuta el fine-tuned contra el mismo benchmark, mismos criterios. Compara cabeza a cabeza: mejora global, por categoría, ejemplos donde ayudó y donde **empeoró** (regresiones).
- [ ] LLM-as-judge ciego: un modelo fuerte compara pares (base, fine-tuned) sin saber cuál es cuál, puntúa 1–5 y razona.
- [ ] Test de catastrophic forgetting: subset de tareas generales (QA común, seguimiento de instrucciones) en ambos modelos. Si cae mucho, ajusta epochs/lr. Documenta el tradeoff.

### Fase 4 — Despliegue (días 10–12)

- [ ] Registra el mejor checkpoint en Unity Catalog como modelo versionado.
- [ ] Despliégalo en un serving endpoint y mide: tokens/segundo, P50/P95, coste por request.
- [ ] Endpoint de comparación A/B: mismo prompt → respuesta del base y del fine-tuned lado a lado con scores.

### Fase 5 — Reproducibilidad (días 12–13)

- [ ] Un comando (job/bundle) re-ejecuta todo: datos → entrenamiento → evaluación → registro.
- [ ] Informe de experimento: por qué fine-tuning y no few-shot/RAG, estadísticas del dataset, sweep, comparativa con ejemplos concretos, análisis de forgetting.

### Fase 6 — Pulido (días 13–14)

- [ ] Demo ≤4 min: el base fallando en tu dominio, el fine-tuned acertando, curvas MLflow, comparación A/B.
- [ ] Titular: "fine-tuneé [modelo] en [dominio] y mejoré de X% a Y% manteniendo Z% de capacidad general" — cada número trazable a datos del repo.

## Criterios de éxito

- [ ] Mejora medible y significativa sobre el baseline en TU benchmark (no solo en loss).
- [ ] Regresiones identificadas y documentadas (ningún fine-tuning mejora todo).
- [ ] Pipeline re-ejecutable de punta a punta.

## Datos y URLs

- Dataset: el tuyo, curado por ti (es el corazón del ejercicio). Si necesitas texto fuente: datasets públicos de [../docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md).
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → Foundation Model Training).

## ⚠️ Seguridad

- No entrenes con datos personales o con licencia incompatible con publicar resultados.
- Foundation Model Training tiene coste: estima antes de lanzar y usa datasets pequeños primero.
