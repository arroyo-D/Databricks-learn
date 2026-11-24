# Preguntas de práctica — ml-01 / Anomalías SMAP/MSL + Exoplanetas

**P1.** ¿Por qué el LSTM de anomalías se entrena SOLO con datos normales?
- A) Porque las anomalías son pocas
- B) Aprende a predecir el comportamiento normal: el error alto de predicción ES la señal de anomalía
- C) Porque no hay GPU
- D) Porque las etiquetas son malas

**P2.** El scoring POR EVENTOS (vs por puntos) existe porque:
- A) Es más fácil de programar
- B) Con 1% de puntos anómalos, predecir "todo normal" da 99% accuracy y 0 anomalías: lo que importa es si detectaste el evento, no cuántos puntos acertaste
- C) Es el estándar de Kaggle
- D) Acelera el entrenamiento

**P3.** El suavizado EWMA del error antes del umbral evita:
- A) El overfitting
- B) Que picos aislados de ruido disparen falsas alarmas: la señal sostenida es lo anómalo
- C) El data leakage
- D) La cuota de Free Edition

**P4.** El umbral dinámico μ + k·σ con k barrido entre 2,5 y 10 te da:
- A) El mejor k por defecto
- B) La curva de trade-off precision/recall por evento para elegir k con criterio (y justificarlo)
- C) Menos datos
- D) Un baseline

**P5.** En Kepler, usar `av_pred_class` como feature sería:
- A) Una buena práctica
- B) Leakage: es una predicción previa del pipeline = la etiqueta disfrazada; el modelo "aprende" la respuesta
- C) Obligatorio
- D) Irrelevante

**P6.** `GroupShuffleSplit` con grupo = `kepid` garantiza:
- A) Más datos en test
- B) Que ninguna estrella aparece en train y test a la vez (varios TCEs de la misma estrella son casi copias: sin el split por grupo, el AUC miente)
- C) Aleatoriedad pura
- D) Estratificación por clase

**P7.** `tce_duration` (horas) vs `tce_period` (días) es la trampa de:
- A) Nulos
- B) Unidades: ratios sin convertir unidades producen features silenciosamente absurdas
- C) Tipos de dato
- D) Encoding

**P8.** ¿Por qué AUC/PR y no accuracy en exoplanetas?
- A) AUC es más bonito
- B) Con clases muy desbalanceadas, un clasificador trivial tiene alta accuracy y no encuentra nada: AUC/PR miden ranking y detección en la clase minoritaria
- C) Porque lo dice sklearn
- D) Porque no hay test set

---

## Soluciones
1. **B** — Predictor de normalidad → error = anomalía.
2. **B** — Los eventos son la unidad de negocio; los puntos engañan.
3. **B** — EWMA filtra ruido; persistencia = señal.
4. **B** — El barrido de k es tu análisis de sensibilidad.
5. **B** — Columnas de vetting previo = respuesta filtrada.
6. **B** — Split por grupo = evaluación honesta en series agrupadas.
7. **B** — Unidades: el bug silencioso clásico.
8. **B** — Desbalanceo ⇒ accuracy prohibida como métrica principal.
