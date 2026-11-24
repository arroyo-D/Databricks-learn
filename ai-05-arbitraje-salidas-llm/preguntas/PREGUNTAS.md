# Preguntas de práctica — ai-05 / Arbitraje multi-crítico de salidas LLM

**P1.** El patrón de arbitraje multi-crítico consiste en:
- A) Varios LLMs votando al azar
- B) Críticos especializados (exactitud, lógica, completitud...) evaluando la salida y un adjudicador decidiendo según sus veredictos
- C) Un LLM más grande
- D) Redundancia de servidores

**P2.** Cada crítico debe evaluar una dimensión:
- A) Todas a la vez
- B) Independiente y específica, con rúbrica propia: críticos genéricos "¿está bien?" no aportan señal
- C) La misma que los demás
- D) Solo gramática

**P3.** El adjudicador resuelve cuando los críticos discrepan:
- A) Promediando siempre
- B) Con reglas explícitas de severidad (un fallo de exactitud pesa más que uno de estilo) y escalado a humano en casos críticos
- C) Ignorando al más negativo
- D) Repitiendo la generación infinitamente

**P4.** AI Gateway delante de los endpoints aporta aquí:
- A) Solo balanceo
- B) Governance del tráfico: rate limits, registro en inference tables de cada veredicto y trazabilidad de costes
- C) Más modelos
- D) Traducción

**P5.** El sistema de arbitraje se evalúa:
- A) No hace falta
- B) Con golden sets de salidas buenas/malas conocidas: ¿detecta los fallos plantados sin rechazar las buenas? (precision/recall del filtro)
- C) Por intuición
- D) Por coste

**P6.** En Free Edition, la versión viable del patrón:
- A) 5 endpoints GPU
- B) Críticos como llamadas `ai_query` pay-per-token + lógica de adjudicación en Python/Delta: mismo patrón, sin serving propio
- C) No se puede
- D) Solo con LangChain cloud

---

## Soluciones
1. **B** — Críticos especializados + adjudicador: separación de criterios.
2. **B** — Rúbricas específicas = señal; "¿está bien?" = ruido.
3. **B** — Severidad explícita y escalado humano.
4. **B** — Gateway = gobierno y trazabilidad del tráfico LLM.
5. **B** — El filtro se mide como clasificador: P/R contra casos conocidos.
6. **B** — Mismo patrón sobre pay-per-token serverless.
