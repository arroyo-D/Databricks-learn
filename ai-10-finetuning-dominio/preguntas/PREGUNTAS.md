# Preguntas de práctica — ai-10 / Fine-tuning en dominio

**P1.** El fine-tuning es la herramienta correcta cuando quieres:
- A) Que el modelo sepa datos de ayer
- B) Cambiar el COMPORTAMIENTO/estilo/formato del modelo (clasificación de dominio, tono, estructura de salida)
- C) Evitar evaluar
- D) Ahorrar tokens siempre

**P2.** Para conocimiento actualizado o documentos específicos, la herramienta correcta es:
- A) Fine-tuning
- B) RAG: el fine-tuning no es una base de datos y "olvidar" datos erróneos aprendidos es casi imposible
- C) Prompts más largos
- D) Más temperatura

**P3.** El dataset de fine-tuning necesita:
- A) Millones de ejemplos de internet
- B) Ejemplos de alta calidad en el formato exacto de la tarea, con split train/validación y deduplicados (cientos buenos > miles mediocres)
- C) Solo prompts de usuario
- D) Datos sintéticos siempre

**P4.** La comparación honesta tras el fine-tuning es:
- A) "Se nota mejor"
- B) Modelo base + prompt vs modelo fine-tuneado en el MISMO golden set: si no gana al prompt bien hecho, el fine-tuning no se justifica
- C) Benchmarks públicos
- D) Coste por token

**P5.** Foundation Model Training en Databricks gestiona:
- A) Solo el cómputo
- B) El entrenamiento gobernado: datos en UC, run en MLflow, modelo resultante registrado en el catálogo
- C) La facturación
- D) Los prompts

**P6.** En tu setup gratuito, la ruta realista para experimentar fine-tuning es:
- A) Foundation Model Training de pago
- B) LoRA de un modelo pequeño en Kaggle/Colab (GPU gratis) con MLflow apuntando al workspace; la versión enterprise se estudia en teoría
- C) No se puede aprender
- D) CPU del MacBook

---

## Soluciones
1. **B** — Fine-tuning = comportamiento; RAG = conocimiento.
2. **B** — Conocimiento fresco → RAG; la regla de decisión nº 1.
3. **B** — Calidad y formato exacto sobre cantidad.
4. **B** — El baseline es el modelo base BIEN prompteado.
5. **B** — Entrenamiento gobernado de punta a punta.
6. **B** — LoRA en GPU gratuita + registro central en MLflow.
