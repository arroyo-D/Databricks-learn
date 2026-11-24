# Preguntas de práctica — cap-02 / Knowledge Assistant

**P1.** Un knowledge assistant "de producción" se diferencia de un demo RAG en:
- A) El modelo usado
- B) Evaluación continua en CI, citas verificadas, dataset que crece desde el tráfico real y gobernanza del corpus
- C) La interfaz
- D) El precio

**P2.** La respuesta "no lo sé" del asistente es:
- A) Un fallo siempre
- B) Una feature: ante ausencia de evidencia en el corpus, admitirlo evita alucinaciones y marca huecos de documentación
- C) Un error de retrieval
- D) Un timeout

**P3.** El golden set de 60+ pares incluye preguntas "sin respuesta" para:
- A) Rellenar
- B) Medir que el sistema sabe abstenerse: un asistente que siempre contesta es un generador de bulos
- C) Probar el teclado
- D) Confundir al LLM

**P4.** La comparativa híbrido vs solo-vectorial con la suite de eval demuestra:
- A) Que híbrido siempre gana
- B) Con números propios, cuánto aporta cada componente: decisiones de arquitectura con evidencia, no por moda
- C) Que BM25 está obsoleto
- D) Nada

**P5.** El job nocturno de ai-13 integrado aquí hace:
- A) Backups
- B) Muestrear logs → detectar preguntas mal respondidas → auto-etiquetar → cola de revisión humana → golden set crece
- C) Limpiar cachés
- D) Apagar clusters

**P6.** En Free Edition este proyecto se construye:
- A) Con el Agent Brick Knowledge Assistant
- B) A mano con Vector Search + MLflow + evaluación: los Bricks no están en Free, y construirlo a mano enseña más
- C) No se puede
- D) Con ChatGPT

---

## Soluciones
1. **B** — Eval, citas, flywheel y gobernanza: producción vs demo.
2. **B** — Abstención = honestidad del sistema.
3. **B** — La abstención también se evalúa.
4. **B** — Arquitectura decidida con evidencia propia.
5. **B** — El flywheel de evaluación en marcha.
6. **B** — El Brick es la versión empaquetada de lo que construyes.
