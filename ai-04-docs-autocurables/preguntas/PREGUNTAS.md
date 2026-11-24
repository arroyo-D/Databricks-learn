# Preguntas de práctica — ai-04 / Documentación autocurable

**P1.** Una documentación "autocurable" es aquella que:
- A) Se escribe sola
- B) Detecta cuándo queda desactualizada respecto al código/datos que describe y propone (o aplica) la corrección
- C) No necesita mantenimiento nunca
- D) Está en PDF

**P2.** El detector de obsolescencia compara típicamente:
- A) El color del tema
- B) Los claims de la doc (nombres de columnas, comandos, salidas de ejemplo) contra el estado real del sistema
- C) La fecha de creación
- D) El autor

**P3.** Vector Search en este proyecto se usa para:
- A) Guardar imágenes
- B) Localizar los fragmentos de documentación semánticamente relacionados con el cambio detectado
- C) Acelerar Git
- D) Traducir

**P4.** La propuesta de corrección automática debe:
- A) Aplicarse siempre sin revisión
- B) Generarse como PR/sugerencia con revisión humana: la confianza en la doc depende de no auto-publicar errores
- C) Enviarse por email
- D) Borrar la doc antigua

**P5.** La GitHub Action del proyecto se dispara lógicamente:
- A) Cada hora
- B) En PRs/merges que tocan código o datos documentados: el cambio es el disparador natural
- C) Solo manualmente
- D) Al hacer login

**P6.** El mayor riesgo del sistema autocurable:
- A) Que sea lento
- B) Falsos positivos (marca como obsoleto lo correcto) o correcciones "plausibles pero falsas": necesita su propia evaluación
- C) Que use Git
- D) El coste de storage

---

## Soluciones
1. **B** — Detección de drift doc↔sistema + propuesta de corrección.
2. **B** — Verificar claims concretos contra la realidad actual.
3. **B** — Búsqueda semántica de fragmentos afectados.
4. **B** — PR con humano en el bucle: nunca auto-publicar.
5. **B** — El merge que cambia lo documentado dispara la revisión.
6. **B** — El autocurador también necesita eval (como ai-01).
