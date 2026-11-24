# Preguntas de práctica — ai-14 / Procesador de documentos (extracción)

**P1.** `ai_parse_document` resuelve:
- A) La traducción
- B) Convertir PDFs/imágenes con layout complejo (tablas, figuras, casillas) en texto estructurado usable por el pipeline
- C) La compresión
- D) El OCR de 1990

**P2.** El patrón de ingesta del procesador es:
- A) Un script manual por documento
- B) Auto Loader vigilando un Volume: cada documento nuevo dispara parseo → extracción → validación → tabla
- C) Email con adjuntos
- D) Copia manual a DBFS

**P3.** La extracción estructurada con LLM necesita:
- A) Libertad total
- B) Un esquema de salida estricto (JSON definido) + validación: lo que no cumple el esquema va a cola de revisión
- C) Temperatura alta
- D) Siempre el modelo más grande

**P4.** La cola de revisión humana para extracciones dudosas:
- A) Es un fallo del sistema
- B) Es diseño: baja confianza → humano; el objetivo es minimizar (no eliminar) la revisión midiendo la precisión por campo
- C) Solo para el jefe
- D) Se hace anual

**P5.** DQX/expectativas sobre los datos extraídos validan:
- A) El layout del PDF
- B) Reglas de negocio sobre los campos extraídos (importes positivos, fechas coherentes, NIF con formato): calidad post-LLM
- C) El tamaño del fichero
- D) El idioma

**P6.** La métrica principal del procesador es:
- A) Documentos/hora solamente
- B) Precisión por campo extraído contra una muestra verificada a mano + % de documentos sin intervención humana (straight-through rate)
- C) Tamaño del modelo
- D) Nº de páginas

---

## Soluciones
1. **B** — Parseo de documentos complejos con layout.
2. **B** — Volume + Auto Loader = fábrica continua, no scripts.
3. **B** — Esquema estricto + cola de excepciones.
4. **B** — Revisión por excepción, medida y minimizada.
5. **B** — Calidad de negocio después de la extracción: el LLM no es la última palabra.
6. **B** — Precisión por campo + tasa de procesamiento directo.
