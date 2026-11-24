# Preguntas de práctica — ai-01 / Detección de regresiones LLM en CI

**P1.** Una "regresión" en un sistema LLM es:
- A) Un error de sintaxis
- B) Un cambio de prompt/modelo/config que empeora la calidad de las respuestas respecto a la versión anterior
- C) Una caída del servidor
- D) Un aumento de coste

**P2.** El golden set de evaluación debe ser:
- A) Generado por el propio LLM
- B) Ejemplos representativos con respuesta esperada, versionados en Delta y revisados por humanos
- C) Los logs de producción crudos
- D) 5 ejemplos fáciles

**P3.** Un "LLM judge" se calibra:
- A) Confiando en él
- B) Comparando sus veredictos contra juicios humanos en una muestra: sin calibración, el judge es otra fuente de error
- C) Subiendo su temperatura
- D) Con más prompts

**P4.** El job de CI bloquea un despliegue cuando:
- A) Siempre
- B) Las métricas de la nueva versión caen por debajo del umbral definido respecto al baseline
- C) El modelo cambia de nombre
- D) Hay más de 10 tests

**P5.** Métrica vs umbral vs baseline en una suite de eval:
- A) Son lo mismo
- B) Métrica = qué mides; baseline = resultado de la versión actual; umbral = cuánta caída toleras antes de bloquear
- C) Baseline = el mejor modelo
- D) Umbral = p-valor

**P6.** ¿Por qué MLflow para la evaluación y no un Excel?
- A) Es más bonito
- B) Cada corrida de eval queda registrada con prompt, modelo, métricas y artefactos: comparables y auditables en el tiempo
- C) Es gratis
- D) Excel no abre JSON

---

## Soluciones
1. **B** — Regresión = empeorar lo que funcionaba.
2. **B** — Golden set = verdad de referencia versionada y humana.
3. **B** — Judge sin calibrar = métrica sin fiabilidad.
4. **B** — Bloqueo por umbral contra baseline: la esencia del CI de comportamiento.
5. **B** — Las tres piezas de cualquier gate de calidad.
6. **B** — Trazabilidad de evals = MLflow.
