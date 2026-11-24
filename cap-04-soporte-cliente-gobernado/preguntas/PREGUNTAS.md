# Preguntas de práctica — cap-04 / Soporte al cliente con IA gobernada

**P1.** "Multi-modelo" en soporte (78% de las empresas según el informe) significa:
- A) Muchos GPUs
- B) Clasificador/enrutador que manda cada ticket al modelo adecuado: triaje barato, casos complejos al modelo potente
- C) Varios proveedores de cloud
- D) Modelos en cascada siempre

**P2.** La caché semántica (ai-07) en soporte ahorra especialmente porque:
- A) Los usuarios escriben bien
- B) Las preguntas frecuentes se repiten con mil redacciones distintas: equivalencia semántica = hits masivos
- C) Es gratis
- D) Los tickets son largos

**P3.** El A/B test de prompts (ai-09) en producción de soporte protege:
- A) El coste
- B) Que un cambio de prompt "que parecía mejor" no degrade respuestas a clientes reales sin evidencia estadística
- C) La latencia
- D) El modelo

**P4.** Los feature flags (ai-12) en este sistema permiten:
- A) Cambiar el logo
- B) Canary de prompts/modelos nuevos sobre un % de tickets con rollback automático si la calidad cae
- C) Apagar el soporte
- D) Traducir

**P5.** La métrica de negocio principal NO es la precisión del modelo sino:
- A) Tokens usados
- B) Resolución al primer contacto, tiempo de respuesta y CSAT: la IA es el medio, no el fin
- C) Modelos desplegados
- D) Prompts versionados

**P6.** Gobernar los datos de clientes en este proyecto implica:
- A) Nada especial
- B) PII en tablas gobernadas UC, acceso mínimo necesario, y los datos de tickets nunca a providers externos sin pasar por el gateway
- C) Cifrado manual
- D) Anonimizar todo siempre

---

## Soluciones
1. **B** — Cada ticket al modelo adecuado: coste y calidad optimizados.
2. **B** — FAQs con redacciones infinitas = paraíso de la caché semántica.
3. **B** — Ningún cambio a clientes sin evidencia.
4. **B** — Canary + rollback automático en producción real.
5. **B** — Métricas de negocio mandan; la IA es medio.
6. **B** — PII gobernada y tráfico externo solo vía gateway.
