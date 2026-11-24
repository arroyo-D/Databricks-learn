# SOLUCIÓN — ML-02 (léela DESPUÉS de intentarlo)

## Parte A — Datos y máscaras

```python
from datasets import load_dataset
ds = load_dataset("hassanjbara/AI4MARS")
```

**La regla de oro:** antes de cualquier loss o métrica, aplica

```python
IGNORE = 255
mask[(rover_mask > 0) | (range_mask > 0)] = IGNORE
```

Si no lo haces, el modelo aprende a "segmentar" las partes del rover y la franja lejana sin etiquetar fiable, y tus métricas se contaminan con píxeles que los autores del dataset dijeron explícitamente que no cuentan.

## Parte B — Modelo y loss

```python
import torch, torch.nn as nn
from torchvision.models.segmentation import deeplabv3_resnet50

model = deeplabv3_resnet50(weights="DEFAULT")
model.classifier[4] = nn.Conv2d(256, 4, kernel_size=1)   # 4 clases
model.aux_classifier[4] = nn.Conv2d(256, 4, kernel_size=1)

# Pesos de clase = inverso de la frecuencia de píxeles (medido en train)
shares = torch.tensor([0.60, 0.20, 0.15, 0.05])          # ejemplo: MIDELO tú
w = 1.0 / shares
w = w / w.sum()
criterion = nn.CrossEntropyLoss(weight=w, ignore_index=255)
```

**Errores comunes:**
- Olvidar `ignore_index=255`: el 255 se convierte en una "quinta clase" fantasma que destroza la loss.
- Pesos de clase copiados de un tutorial en vez de medidos en TU split de train.
- Data augmentation con rotaciones fuertes o flips verticales: el cielo de Marte no está abajo. Flips horizontales sí; lo demás, piénsalo físicamente.
- Batch size enorme en una T4: con imágenes ~512px, batch 8–16 es lo realista en GPU gratis; sube el acumulado de gradiente si quieres batch efectivo mayor.

## Parte C — IoU por clase

```python
import numpy as np

def iou_por_clase(pred, target, n_clases=4, ignore=255):
    ious = {}
    for c in range(n_clases):
        p, t = (pred == c), (target == c)
        p[target == ignore] = False
        union = (p | t).sum()
        if union == 0:
            continue                      # clase ausente en esta imagen
        ious[c] = (p & t).sum() / union
    return ious
```

Promedia por imagen y luego sobre el test. Reporta una tabla:

| clase | IoU baseline (todo-suelo) | IoU modelo |
|---|---|---|
| soil | ~0.6 | ... |
| bedrock | 0.0 | ... |
| sand | 0.0 | ... |
| big rock | 0.0 | ... |

El baseline "todo suelo" da mIoU bajísimo aunque su pixel accuracy parezca alta: **esa tabla es tu defensa de por qué IoU y no accuracy**.

## Parte D — Demo Gradio

```python
import gradio as gr

PALETA = [(200,160,120),(120,120,120),(240,220,130),(60,60,60)]  # soil/bedrock/sand/rock

def segmentar(img):
    pred = inferir(img)                      # tu modelo cargado
    out = np.zeros((*pred.shape, 3), dtype=np.uint8)
    for c, color in enumerate(PALETA):
        out[pred == c] = color
    return out

gr.Interface(segmentar, gr.Image(label="Foto marciana"),
             gr.Image(label="Terreno segmentado"),
             title="Mars Terrain Segmentation (AI4MARS)",
             description="soil / bedrock / sand / big rock — modelo DeepLabV3+ entrenado con AI4MARS (NASA/JPL-Caltech)"
             ).launch()
```

En un Space de HF: `app.py` + `requirements.txt` (`torch`, `gradio`) y listo. El `.pt` del modelo súbelo al repo del Space (o cárgalo desde MLflow con los secretos en *Settings → Secrets* del Space).

## Qué decir en una entrevista

> "Entrené DeepLabV3+ sobre AI4MARS respetando las máscaras oficiales de rover y rango como ignore, con pesos de clase medidos en el train, y evalué con IoU por clase contra un baseline trivial para demostrar por qué la pixel accuracy engaña; la demo en Gradio está desplegada en HF Spaces."
