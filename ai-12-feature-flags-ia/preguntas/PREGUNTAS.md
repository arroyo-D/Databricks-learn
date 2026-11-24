# Preguntas de práctica — ai-12 / Feature flags para sistemas de IA

**P1.** Un feature flag en un sistema de IA controla típicamente:
- A) El color de la UI
- B) Qué modelo/prompt/config sirve cada petición en runtime, sin redesplegar
- C) El idioma del usuario
- D) El cluster de cómputo

**P2.** El despliegue canary de un modelo nuevo significa:
- A) Desplegarlo de golpe
- B) Enviarle un % pequeño del tráfico mientras se comparan métricas con la versión estable, ampliando solo si no degrada
- C) Probarlo en local
- D) Desplegar de noche

**P3.** El rollback automático se dispara cuando:
- A) Alguien lo pulsa
- B) Las métricas monitorizadas (calidad, latencia, tasa de error) cruzan el umbral definido: el flag vuelve solo a la versión estable
- C) Termina el día
- D) El modelo se aburre

**P4.** Por qué el rollback debe ser automático y no manual:
- A) Por pereza
- B) Un modelo degradado daña usuarios cada minuto; la detección-monitorización-reacción humana es demasiado lenta a las 3 AM
- C) Es más barato
- D) Lo exige GDPR

**P5.** La combinación flag + métricas de calidad en streaming requiere:
- A) Solo un if en el código
- B) Telemetría por variante en cada respuesta + evaluación continua + regla de decisión documentada ANTES del despliegue
- C) Un dashboard anual
- D) Permisos de admin

**P6.** El riesgo de muchos flags activos a la vez:
- A) Ninguno
- B) Combinatoria inmanejable y deuda técnica: los flags tienen fecha de caducidad y se limpian
- C) Más coste de disco
- D) Son ilegales

---

## Soluciones
1. **B** — Config de IA como dato en runtime, no como despliegue.
2. **B** — Canary = riesgo acotado con evidencia creciente.
3. **B** — Umbral → rollback automático.
4. **B** — MTTR de minutos, no de horas: la degradación de IA es continua.
5. **B** — Telemetría por variante + decisión predefinida.
6. **B** — Flags con caducidad: la deuda de flags es real.
