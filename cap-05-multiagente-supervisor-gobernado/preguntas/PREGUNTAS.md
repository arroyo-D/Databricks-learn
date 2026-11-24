# Preguntas de práctica — cap-05 / Multiagente supervisor gobernado

**P1.** El Supervisor Agent es el caso de uso nº 1 de Agent Bricks porque:
- A) Es el más nuevo
- B) Las peticiones reales de negocio son compuestas: descomponer, delegar en especialistas y ensamblar es el patrón natural
- C) Es el más barato
- D) Es el más simple

**P2.** El panel de críticos (ai-05) revisa la respuesta del supervisor antes de entregar para:
- A) Retrasarla
- B) Cazar errores de exactitud/lógica/completitud que el generador no ve en su propia salida (auto-crítica ciega)
- C) Justificar el coste
- D) Generar más texto

**P3.** La cola humana para acciones sensibles existe porque:
- A) Los agentes son lentos
- B) Autonomía acotada: leer y resumir es libre; escribir, enviar o borrar requiere aprobación humana por diseño
- C) Es un requisito legal siempre
- D) El supervisor se cansa

**P4.** MLflow Tracing en el sistema completo permite reconstruir:
- A) Solo el resultado
- B) Cada decisión del supervisor, cada tool call, cada crítica y cada escalado: auditoría completa de una decisión no determinista
- C) Los costes solamente
- D) El prompt final

**P5.** Las UC Functions como herramientas de los especialistas garantizan que:
- A) Sean rápidas
- B) El agente solo accede a datos con los permisos que ya tiene gobernados: la IA hereda la gobernanza, no la salta
- C) Sean pocas
- D) No fallen nunca

**P6.** Según el State of AI Agents, las empresas con gobernanza activa llevan a producción 12x más proyectos porque:
- A) Gobernar es divertido
- B) La gobernanza reduce el riesgo percibido: los stakeholders aprueban lo que pueden auditar, limitar y revertir
- C) Tienen más presupuesto
- D) Usan mejores modelos

---

## Soluciones
1. **B** — Peticiones compuestas = supervisor + especialistas.
2. **B** — El generador no ve sus propios errores: críticos independientes.
3. **B** — Autonomía por diseño: lectura libre, acción sensible aprobada.
4. **B** — Auditoría de extremo a extremo de lo no determinista.
5. **B** — La gobernanza del dato se hereda, no se evade.
6. **B** — Confianza auditable = más proyectos aprobados.
