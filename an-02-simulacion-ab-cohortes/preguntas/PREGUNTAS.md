# Preguntas de práctica — an-02 / A/B testing y cohortes

**P1.** En un A/B test, la unidad de análisis correcta es:
- A) El evento
- B) El usuario (o la unidad de aleatorización): usar eventos infla n y rompe la independencia
- C) La sesión siempre
- D) El día

**P2.** Un SRM (Sample Ratio Mismatch) significa:
- A) Que el test ganó
- B) Que el reparto observado entre variantes se desvía del esperado más de lo que el azar permite: hay un bug y el experimento no es válido
- C) Que falta potencia
- D) Que hay estacionalidad

**P3.** Parar un test "en cuanto da p < 0,05" (peeking):
- A) Es eficiente
- B) Multiplica los falsos positivos (puede pasar del 5% al 20-30%): se fija n o un horizonte ANTES de empezar
- C) Es lo recomendado
- D) Solo afecta a tests pequeños

**P4.** Potencia estadística del 80% significa:
- A) 80% de probabilidad de que H0 sea cierta
- B) 80% de probabilidad de detectar el efecto SI existe realmente con el tamaño muestral elegido
- C) 80% de significancia
- D) 80% del tráfico al test

**P5.** El lift correcto de reportar es:
- A) Solo relativo ("+16,7% de conversión")
- B) Absoluto Y relativo juntos (+2 pp sobre 12% = +16,7% relativo): uno sin el otro engaña
- C) Solo absoluto
- D) El p-valor basta

**P6.** La cohorte de un usuario se define por:
- A) Su país
- B) Su período de registro/alta (semana o mes): se sigue a ese grupo a lo largo del tiempo
- C) Su dispositivo
- D) Su última compra

**P7.** En un triángulo de retención, que las curvas "se aplanen" (flattening) indica:
- A) Un bug
- B) Que queda un núcleo fiel de usuarios que no abandona (buena señal de product-market fit)
- C) Que nadie usa el producto
- D) Estacionalidad

**P8.** Un guardrail metric en un test sirve para:
- A) Decorar el informe
- B) Detectar que la variante mejora la métrica objetivo a costa de romper otra cosa importante (latencia, quejas, otra métrica clave)
- C) Aumentar la potencia
- D) Reducir el SRM

---

## Soluciones
1. **B** — Unidad de aleatorización = unidad de análisis.
2. **B** — SRM = asignación rota; invalida todo lo demás.
3. **B** — Peeking infla falsos positivos: horizonte fijado ex ante.
4. **B** — Potencia = detectar el efecto si existe.
5. **B** — Absoluto + relativo, siempre juntos.
6. **B** — Cohorte = grupo por período de entrada.
7. **B** — Aplanamiento = núcleo retenido estable.
8. **B** — Guardarraíles: ganar sin romper nada.
