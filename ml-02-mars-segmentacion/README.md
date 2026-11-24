# ML-02 — Segmentación de terreno marciano (AI4MARS)

> Proyecto 2 del *Space ML Project Guides*: segmentación semántica de imágenes reales de los rovers de Marte, con el dataset AI4MARS. Es el único módulo del repo que necesita GPU (o una alternativa gratuita).

## Objetivo

Entrenar un segmentador (DeepLabV3+ con ResNet-50) que clasifique cada píxel de una foto marciana en `soil / bedrock / sand / big rock`, respetando las máscaras oficiales del dataset y midiendo con IoU por clase. Cerrar con una demo interactiva.

## Por qué importa

La segmentación es visión por computador "de verdad": salida densa, clases desbalanceadas, etiquetas con zonas "no evaluar" y métricas por clase donde la pixel accuracy miente (si el 80% de la imagen es suelo, predecir "todo suelo" da 80% de accuracy y un modelo inútil). AI4MARS es un dataset real de la NASA con sus máscaras de rover y rango ya pensadas — aprender a respetar `IGNORE=255` es aprender a leer datasets profesionales. Y una demo donde subes una foto de Marte y ves el mapa de terreno es la pieza de portfolio que más se recuerda.

## Datos (URLs verificadas)

- Dataset AI4MARS (18.100 imágenes train + 322 test): https://huggingface.co/datasets/hassanjbara/AI4MARS ✅
- PyTorch (docs): https://pytorch.org/docs/stable/index.html ✅
- Gradio (demo): https://www.gradio.app/docs ✅
- MLflow: https://docs.databricks.com/ → *MLflow* ✅

## Prerrequisitos

- `ml-01` (MLflow, baselines, honestidad en métricas). PyTorch básico.

## Entorno de cómputo (decide antes de empezar)

| Opción | GPU | Coste | Notas |
|---|---|---|---|
| Cluster Databricks con GPU (p. ej. `g4dn.xlarge` en AWS) | ✅ | 💰 | La opción "nativa": MLflow integrado |
| Google Colab / Kaggle Notebooks | ✅ (T4 gratis) | Gratis | Alternativa válida: entrena allí, registra el modelo en MLflow de Databricks |
| Solo CPU | ❌ inviable | — | No intentes DeepLabV3 en CPU |

## Tareas

### Parte A — Datos

- [ ] Carga el dataset desde Hugging Face (`datasets.load_dataset("hassanjbara/AI4MARS")`) y súbelo a un UC Volume si trabajas en Databricks.
- [ ] Entiende las etiquetas: clases 0=soil, 1=bedrock, 2=sand, 3=big rock, **255=ignore**. Plotea 6 imágenes con su máscara superpuesta.
- [ ] **Aplica las máscaras oficiales**: `rover_mask` (partes del propio rover) y `range_mask` (zona lejana sin etiquetar fiable) → esos píxeles pasan a 255 y NO cuentan en loss ni en métricas.
- [ ] Estadística de clases: % de píxeles por clase. Verás el desbalanceo → lo usas en la Parte B.

### Parte B — Entrenamiento

- [ ] Augmentation básico: flips horizontales, jitter de brillo/contraste (en Marte no giras la imagen 90°, piensa por qué).
- [ ] DeepLabV3+ ResNet-50 (`torchvision.models.segmentation.deeplabv3_resnet50` con pesos ImageNet) adaptando la cabeza a 4 clases.
- [ ] **Loss con class weights** inversos a la frecuencia de píxeles (`weight = 1 / share_clase`, normalizado) y **`ignore_index=255`** en la CrossEntropy.
- [ ] Entrena 20–30 épocas con early stopping sobre val IoU. Log en MLflow: params, loss, IoU por clase por época, y una figura de predicción como artifact.
- [ ] **Baseline tonto obligatorio** (30 min): un modelo que predice siempre "soil". Calcula su IoU por clase. Tu modelo debe ganar en TODAS las clases; si en "big rock" no gana, no has terminado.

### Parte C — Evaluación honesta

- [ ] Métrica: **IoU por clase + mIoU** sobre el test oficial (322 imágenes), ignorando 255. NO reportes pixel accuracy como métrica principal.
- [ ] Análisis de errores: ¿qué clases se confunden? (bedrock↔soil es lo típico). Matriz de confusión de píxeles.
- [ ] 4–6 figuras: imagen / máscara real / predicción, incluyendo al menos un fallo claro del modelo.

### Parte D — Demo

- [ ] App Gradio: subir imagen → mapa de segmentación coloreado + leyenda de clases. (`gr.Interface` con `gr.Image`).
- [ ] Despliegue: Hugging Face Spaces (gratis, https://huggingface.co/docs/hub/spaces ✅) o Databricks Apps con Model Serving. El modelo se registra en MLflow Model Registry y se carga desde ahí.
- [ ] README del módulo: resumen de 1 línea, tabla baseline-vs-modelo, la mejor figura, enlace a la demo y sección *Limitations* (p. ej. entrenado solo con imágenes diurnas de Curiosity).

## Criterios de éxito

- [ ] `ignore_index=255` aplicado en loss y métricas (demostrable en código).
- [ ] Class weights justificados con la estadística de píxeles de la Parte A.
- [ ] IoU por clase en el test oficial + comparación con el baseline tonto.
- [ ] Demo funcionando con enlace y README con la tabla y las limitaciones.

## Recursos

- Fuentes de datos: [../docs/fuentes-de-datos.md](../docs/fuentes-de-datos.md)
- Si entrenas en Colab/Kaggle y registras en Databricks MLflow: https://docs.databricks.com/ → *MLflow tracking desde fuera del workspace* ✅

## ⚠️ Seguridad

- Sin secretos especiales (HF público). Si usas token de HF para subir la demo, va en Secrets del Space, no en el código.
- Imágenes de dominio público de la NASA: cita "NASA/JPL-Caltech / AI4MARS" en la demo.

## Entregable

Notebook de entrenamiento + runs MLflow + demo desplegada + README con tabla comparativa y figuras.
