# Preguntas de práctica — ml-02 / Segmentación AI4MARS

**P1.** `ignore_index=255` en la CrossEntropy sirve para:
- A) Ignorar errores
- B) Excluir de la loss los píxeles marcados como "no evaluar" (rover, zona lejana): sin él, el 255 se convierte en una quinta clase fantasma
- C) Acelerar el entrenamiento
- D) Reducir memoria

**P2.** La pixel accuracy engaña en este dataset porque:
- A) Las imágenes son grandes
- B) Si el 60-80% de los píxeles son "soil", predecir todo suelo da alta accuracy con un modelo inútil: por eso se mide IoU por clase
- C) Hay 4 clases
- D) Es un dataset sintético

**P3.** Los class weights inversos a la frecuencia de píxeles:
- A) Se copian de un tutorial
- B) Se miden en TU split de train y compensan el desbalanceo para que "big rock" (raro) no sea ignorado por la loss
- C) Son siempre 1/4
- D) Solo se usan en NLP

**P4.** Flips verticales como augmentation en Marte:
- A) Siempre bien
- B) No tienen sentido físico (el cielo no está abajo): augmentation debe respetar la física del dominio
- C) Mejoran el IoU
- D) Son obligatorios

**P5.** El baseline "todo-suelo" se calcula para:
- A) Perder tiempo
- B) Demostrar con números que tu modelo gana en TODAS las clases y justificar IoU frente a accuracy
- C) Rellenar el README
- D) Comparar GPUs

**P6.** IoU (Intersection over Union) de una clase mide:
- A) Píxeles correctos / total
- B) Solape entre predicción y ground truth dividido por su unión: penaliza tanto falsos positivos como falsos negativos
- C) La correlación entre clases
- D) El área de la clase

**P7.** Las máscaras `rover_mask` y `range_mask` se aplican porque:
- A) Son bonitas
- B) Los autores del dataset marcaron esas zonas como no etiquetadas fiables: respetarlas ES leer el dataset profesionalmente
- C) Reducen cómputo
- D) Mejoran la accuracy

**P8.** El modelo entrenado en Kaggle/Colab llega a producción/portfolio vía:
- A) Copiar el .pt por email
- B) Registro en MLflow Model Registry (de Databricks) + demo en HF Spaces que lo carga
- C) Git LFS en el repo
- D) No se puede

---

## Soluciones
1. **B** — Ignore index: la regla de oro de datasets con zonas no evaluables.
2. **B** — Accuracy global inútil con desbalanceo extremo: IoU por clase.
3. **B** — Pesos medidos en tu train, no copiados.
4. **B** — Augmentation coherente con la física del dominio.
5. **B** — Baseline trivial = tu vara de medir honesta.
6. **B** — IoU = solape/unión: la métrica estándar de segmentación.
7. **B** — Respetar las máscaras oficiales = respetar el dataset.
8. **B** — Registry central + demo desplegada.
