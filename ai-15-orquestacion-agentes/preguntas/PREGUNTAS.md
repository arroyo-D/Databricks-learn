# Preguntas de práctica — ai-15 / Orquestación multi-agente

**P1.** Un agente se diferencia de una chain porque:
- A) Usa más tokens
- B) Decide dinámicamente qué herramienta usar y cuándo parar, en un bucle observación→decisión→acción
- C) Tiene mejor prompt
- D) Es más rápido

**P2.** Las UC Functions como herramientas del agente aportan:
- A) Más código
- B) Capacidades (SQL, cálculos) gobernadas con permisos de Unity Catalog: el agente hereda la gobernanza del dato
- C) Gráficos
- D) Velocidad

**P3.** MLflow Tracing en un sistema multi-agente es crítico porque:
- A) Queda bien
- B) Sin trazas de cada llamada agente→herramienta→agente, depurar un sistema no determinista es adivinanza
- C) Es obligatorio
- D) Reduce coste

**P4.** Un límite de iteraciones/pasos en el bucle del agente existe para:
- A) Ahorrar disco
- B) Evitar bucles infinitos y gasto descontrolado de tokens cuando el agente no converge
- C) Cumplir una norma
- D) Mejorar la calidad

**P5.** El escalado a humano (human-in-the-loop) se decide por:
- A) Aleatoriedad
- B) Reglas explícitas: acciones sensibles (escrituras, envíos, pagos) o baja confianza → aprobación humana antes de ejecutar
- C) Horario
- D) El modelo que toque

**P6.** El patrón supervisor + especialistas gana a un agente único cuando:
- A) Siempre
- B) Las subtareas son heterogéneas (datos, documentos, redacción) y cada especialista puede tener herramientas/prompts optimizados
- C) Hay mucho presupuesto
- D) El problema es simple

---

## Soluciones
1. **B** — Decisión dinámica en bucle: la esencia de agente.
2. **B** — Herramientas con la gobernanza UC heredada.
3. **B** — No determinista + multi-paso = tracing o nada.
4. **B** — Cotas de seguridad contra bucles y gasto.
5. **B** — Reglas de escalado explícitas: autonomía acotada.
6. **B** — Especialización cuando las subtareas difieren.
