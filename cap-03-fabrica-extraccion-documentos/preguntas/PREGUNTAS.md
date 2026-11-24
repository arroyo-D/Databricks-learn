# Preguntas de práctica — cap-03 / Fábrica de extracción de documentos

**P1.** Una "fábrica" de documentos (vs un script de parseo) implica:
- A) Más PDFs
- B) Ingesta continua (Auto Loader), extracción con esquema estricto, validación, cola de revisión y métricas de precisión por campo
- C) Una carpeta compartida
- D) OCR básico

**P2.** Information Extraction es el caso de uso destacado del State of AI Agents porque:
- A) Es fácil
- B) Montañas de documentos no estructurados (facturas, contratos, partes) se convierten en datos consultables: ROI directo
- C) Es nuevo
- D) No necesita evaluación

**P3.** La confianza por campo en la extracción permite:
- A) Colorear la UI
- B) Enrutar: alta confianza → directo a tabla; baja → revisión humana. La automatización se mide por la tasa de paso directo
- C) Ahorrar tokens
- D) Ordenar documentos

**P4.** El golden set de la fábrica se construye con:
- A) Salidas del propio LLM
- B) Documentos reales (sintéticos aquí) verificados campo a campo a mano: la verdad de referencia de la precisión
- C) Otro LLM
- D) Estadísticas

**P5.** Forense (ai-03) aplicado a la fábrica responde:
- A) La hora
- B) Por qué un tipo de documento falla sistemáticamente: ¿parseo, prompt, esquema o validación? Cada causa tiene reparación distinta
- C) Quién lo rompió
- D) El coste exacto

**P6.** Las expectativas/DQX después del LLM existen porque:
- A) El LLM se equivoca poco
- B) La extracción puede ser "plausible pero falsa": las reglas de negocio son la última línea antes de que el dato llegue a Gold
- C) Son obligatorias
- D) Aceleran el pipeline

---

## Soluciones
1. **B** — Fábrica = pipeline continuo con calidad medida, no script.
2. **B** — Documentos → datos consultables: el ROI más directo de la IA.
3. **B** — Confianza como router automático/humano.
4. **B** — Verdad manual por campo: la base de toda métrica.
5. **B** — Taxonomía de fallos → reparación dirigida.
6. **B** — Reglas de negocio tras el LLM: plausible ≠ correcto.
