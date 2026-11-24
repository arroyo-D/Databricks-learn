# Preguntas de práctica — an-03 / Sentimiento + extracción con LLM

**P1.** Las 100 gold labels etiquetadas a mano sirven para:
- A) Rellenar el dataset
- B) Evaluar objetivamente qué método (VADER/LLM) funciona mejor EN TU dominio: sin referencia no hay evaluación, solo opiniones
- C) Entrenar un transformer
- D) Cumplir el checklist

**P2.** VADER es una buena elección cuando:
- A) Necesitas matices de sarcasmo en español
- B) Necesitas latencia cero, coste cero y textos cortos con sentimiento extremo (y en inglés funciona mejor que en español)
- C) Siempre
- D) Nunca

**P3.** `ai_query` frente a llamar a la API de un LLM desde el notebook:
- A) Es lo mismo
- B) Integra el LLM en SQL/Delta gobernado (UC, lineage, batch), sin gestionar claves externas ni mover datos fuera
- C) Es más lento siempre
- D) Solo funciona con GPT

**P4.** Antes de parsear el JSON que devuelve el LLM hay que:
- A) Confiar en el formato
- B) Guardar la respuesta CRUDA en Delta: si el parseo falla o mejora el prompt, no pierdes el dato ni repites llamadas
- C) Validar con regex
- D) Nada

**P5.** Si el 15% de las respuestas del LLM no parsean como JSON válido, el problema suele ser:
- A) El modelo es tonto
- B) El prompt: falta especificar el esquema exacto y ejemplos; se ajusta y se reejecuta sobre la respuesta cruda
- C) La base de datos
- D) La temperatura del cluster

**P6.** NMF sobre TF-IDF para temas produce:
- A) Clusters de documentos
- B) Temas como combinaciones de palabras con pesos: interpretables revisando las top-palabras de cada componente
- C) Embeddings
- D) Traducciones

**P7.** Deduplicar ofertas de empleo que reaparecen semana a semana:
- A) `dropDuplicates()` sin más
- B) `MERGE` por clave (url) conservando primera/última aparición: así no pierdes el histórico de cuánto tiempo estuvo publicada
- C) No deduplicar
- D) Por título solamente

**P8.** Una review de 3 estrellas "me encanta pero se cierra solo" es el caso que prueba:
- A) Que el score numérico basta
- B) Que necesitas tu propio criterio de etiquetado (mixta) y por eso las gold labels no son el score de Google Play
- C) Que VADER siempre falla
- D) Que el LLM siempre acierta

---

## Soluciones
1. **B** — Gold labels = la única verdad de referencia que vale.
2. **B** — VADER: gratis e instantáneo; fuerte en extremos, débil en matices/ES.
3. **B** — LLM dentro del pipeline gobernado.
4. **B** — Respuesta cruda guardada = reejecución gratis.
5. **B** — JSON roto = prompt mal especificado, no modelo malo.
6. **B** — NMF = temas interpretables por top-palabras.
7. **B** — MERGE con historia de apariciones.
8. **B** — Tu criterio de etiquetado manda, no el score de la plataforma.
