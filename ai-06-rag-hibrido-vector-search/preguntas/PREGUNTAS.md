# Preguntas de práctica — ai-06 / RAG híbrido con Vector Search

**P1.** La búsqueda híbrida combina:
- A) Dos LLMs
- B) Similitud vectorial (semántica) + keyword/BM25 (términos exactos): cada una cubre los fallos de la otra
- C) Dos bases de datos
- D) SQL y Python

**P2.** La búsqueda puramente vectorial falla típicamente con:
- A) Sinónimos
- B) Identificadores exactos, códigos de error, nombres propios raros: donde la palabra literal importa más que el significado
- C) Textos largos
- D) Preguntas complejas

**P3.** El chunking estructural (por secciones del documento) se prefiere al de tamaño fijo porque:
- A) Es más rápido
- B) Respeta la unidad semántica: un chunk que corta una definición por la mitad recupera fragmentos inútiles
- C) Usa menos tokens siempre
- D) Es lo único que soporta Delta

**P4.** Las "citas verificadas" del RAG significan:
- A) Que el LLM dice la fuente
- B) Que el sistema comprueba que cada afirmación [1][2] se sostiene realmente en el chunk citado (no basta con que el LLM la escriba)
- C) URLs externas
- D) Bibliografía

**P5.** La métrica de retrieval relevante a evaluar es:
- A) Latencia solamente
- B) Si el chunk correcto está entre los k recuperados (recall@k) y si la respuesta generada es fiel a ellos (faithfulness)
- C) El tamaño del índice
- D) El nº de vectores

**P6.** En Free Edition, Vector Search está limitado a:
- A) Ilimitado
- B) 1 endpoint con 1 unidad: se reutiliza el mismo endpoint entre proyectos y se diseña con esa restricción
- C) 10 endpoints
- D) No existe

---

## Soluciones
1. **B** — Semántica + literal: complementariedad.
2. **B** — Términos exactos = talón de Aquiles del embedding.
3. **B** — El chunk es la unidad de recuperación: cortarlo mal rompe todo lo demás.
4. **B** — Verificar que la cita sostiene la afirmación: anti-alucinación estructural.
5. **B** — recall@k + faithfulness: retrieval y generación se evalúan por separado.
6. **B** — Un endpoint compartido: restricción de diseño en Free.
