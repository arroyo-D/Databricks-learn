# Preguntas de práctica — ai-09 / Prompt registry + A/B testing de prompts

**P1.** Un prompt registry (en Unity Catalog) aporta:
- A) Bonitos nombres
- B) Versionado gobernado de prompts como artefactos: quién cambió qué, cuándo, con permisos y rollback
- C) Más tokens gratis
- D) Autocompletado

**P2.** Un prompt en producción debe referenciarse por:
- A) Copiar-pegar en el código
- B) Nombre + alias/versión del registry: el código no cambia al promocionar una versión nueva
- C) Hash manual
- D) Variable de entorno

**P3.** El A/B test de prompts compara:
- A) Latencias
- B) Métricas de calidad de las respuestas por variante sobre el mismo tráfico/golden set, con significancia estadística
- C) Longitud de los prompts
- D) Autores

**P4.** Un prompt "mejor en los ejemplos" pero sin test estadístico:
- A) Se despliega
- B) Es una anécdota: sin golden set + comparación con potencia suficiente, no sabes si la mejora es real o ruido
- C) Se versiona como final
- D) Es culpa del modelo

**P5.** El rollback de un prompt problemático con registry es:
- A) Reescribirlo de memoria
- B) Cambiar el alias a la versión anterior: segundos, sin desplegar código
- C) Borrar el endpoint
- D) Llamar a soporte

**P6.** scipy entra en este proyecto para:
- A) Hacer gráficos
- B) Los tests estadísticos (proporciones/medias) que deciden si la variante B realmente supera a la A
- C) Conectar con Databricks
- D) Generar prompts

---

## Soluciones
1. **B** — Prompts como artefactos gobernados, no strings sueltos.
2. **B** — Alias del registry = despliegue sin cambio de código.
3. **B** — Calidad medida con estadística, no por impresión.
4. **B** — Anécdota vs evidencia: la línea que separa hobby de ingeniería.
5. **B** — Rollback = mover el alias.
6. **B** — Decisión estadística sobre las variantes.
