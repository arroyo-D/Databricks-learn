# SOLUCIÓN — ML-01 (léela DESPUÉS de intentarlo)

## Parte A — Anomalías SMAP/MSL

### Arquitectura mínima

```python
import torch.nn as nn

class LSTMPredictor(nn.Module):
    def __init__(self, n_features=1, hidden=80):
        super().__init__()
        self.lstm = nn.LSTM(n_features, hidden, num_layers=2,
                            batch_first=True, dropout=0.3)
        self.head = nn.Linear(hidden, n_features)
    def forward(self, x):               # x: (batch, 250, features)
        out, _ = self.lstm(x)
        return self.head(out[:, -1])    # predice el siguiente paso
```

### Umbral dinámico y scoring por eventos (el corazón del ejercicio)

```python
import numpy as np, pandas as pd

err = np.abs(y_true - y_pred)
err_s = pd.Series(err).ewm(span=30).mean().values      # EWMA suavizado
mu, sd = err_s.mean(), err_s.std()
k = 5.0                                                # barrer 2.5 → 10
flags = err_s > mu + k * sd
```

**Scoring por eventos:** convierte `flags` en intervalos contiguos. Un intervalo predicho es TP si se solapa con algún intervalo etiquetado (y marca esa anomalía como encontrada); si no, es FP. Recall = anomalías etiquetadas encontradas / total etiquetadas. Además aplica *pruning*: si dos FP consecutivos están a menos de N puntos, funde o descarta el más corto — es lo que hace el paper para no penalizar rachas.

**Errores comunes:**
- Entrenar con datos anómalos: el LSTM aprende a predecir lo normal; si entrena con anomalías, las "aprende" y deja de detectarlas.
- Accuracy por puntos: con 1% de puntos anómalos, predecir "todo normal" da 99% de accuracy y 0 anomalías detectadas. Por eso el scoring es por eventos.
- Umbral fijo sobre el error crudo: sin EWMA, cualquier pico de ruido dispara. Suaviza primero.
- No barrer k: el k óptimo cambia por canal; un barrido 2.5→10 con F1 por eventos te da el trade-off P/R y es material de entrevista.
- El z-score baseline gana en canales con anomalías de "pico aislado"; el LSTM gana en anomalías de cambio de régimen. Saber decir esto vale más que el F1.

## Parte B — Exoplanetas Kepler

### Las 3 trampas (caen todos)

1. **Leakage de columnas**: `av_pred_class`, `av_pp_*` y cualquier score del pipeline de vetting son predicciones previas = la etiqueta filtrada. Descártalas y lista en tu README las columnas eliminadas.
2. **Unidades**: `tce_duration` en horas vs `tce_period` en días. Convierte duración a días (o periodo a horas) antes de calcular ratios como `duracion/periodo`.
3. **Flatten sin máscara**: `lc.flatten()` estima la tendencia con el tránsito dentro y se lo come. Enmascara primero los tránsitos conocidos (`lc.mask_transits()` o máscara manual por periodo/época/duración) y luego flattens.

### Split por estrella

```python
from sklearn.model_selection import GroupShuffleSplit
gss = GroupShuffleSplit(n_splits=1, test_size=0.2, random_state=42)
tr, te = next(gss.split(X, y, groups=df["kepid"]))
assert not set(df.kepid[tr]) & set(df.kepid[te])   # cero estrellas compartidas
```

### Métrica

`roc_auc_score` y curva Precision-Recall (`average_precision_score`). NO accuracy: con 10% de positivos, un clasificador trivial acierta el 90% y no encuentra ni un planeta.

### CNN de dos vistas (esquema)

Vista global: curva plegada al periodo, binneada a ~2001 puntos. Vista local: zoom alrededor del tránsito, ~201 puntos. Dos ramas Conv1D (conv→pool×3) que concatenan y una densa. Mismo split por estrella que el baseline, siempre.

**Lo que debe salir:** el baseline tabular ya da un AUC decente (~0,9) porque las features del TCE resumen la física; la CNN gana unos puntos pero cuesta 10x. Esa frase, con números tuyos, es tu bullet de portfolio.

## MLflow

```python
import mlflow
mlflow.set_experiment("/Users/tu@email/ml-01-nasa")
with mlflow.start_run(run_name="lstm_k5"):
    mlflow.log_params({"L": 250, "dropout": 0.3, "k": 5.0})
    mlflow.log_metrics({"precision_eventos": p, "recall_eventos": r})
    mlflow.log_figure(fig, "deteccion_canal_A-4.png")
```

## Qué decir en una entrevista

> "Entrené un LSTM sobre telemetría real de SMAP/MSL con umbral dinámico sobre error suavizado y scoring por eventos, y un clasificador de exoplanetas con split por estrella y control de leakage de columnas; en ambos casos comparé contra un baseline simple en MLflow y solo me quedé con el deep learning donde ganaba de verdad."
