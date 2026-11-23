# cap-04 · Centro de soporte al cliente con IA gobernada (coste + calidad + rollout seguro)

> **Proyecto integrador IA** — une `ai-02` (cost autopilot), `ai-07` (caché semántica), `ai-09` (A/B testing) y `ai-12` (feature flags) en un único servicio de producción.
> Basado en *State of AI Agents*: la clasificación y enrutado de consultas de soporte es uno de los **top 3 casos de uso** empresariales, y el 40% del total se centra en experiencia de cliente. El **78%** de las empresas usa 2+ familias de modelos — este proyecto te enseña a operar así.

**Nivel:** 🔴 · **Duración estimada:** 3–4 semanas
**Integra:** `ai-02`, `ai-07`, `ai-09`, `ai-12`

## El escenario

El servicio de soporte de la empresa recibe miles de consultas. Vas a construir el servicio de IA que las **clasifica, resume y sugiere respuesta**, operado como un producto serio:

- **Económico**: enrutado por complejidad al modelo más barato suficiente (`ai-02`) + caché semántica para repetidas (`ai-07`).
- **Medible**: cada cambio de prompt/modelo se despliega como experimento A/B con significancia estadística (`ai-09`).
- **Seguro**: rollout gradual con rollback automático si la calidad cae (`ai-12`).

## Arquitectura objetivo

```
consulta soporte ──► serving endpoint (pyfunc)
   │
   ├─ flag_client: ¿variante baseline o experimental? (rollout gradual)
   ├─ caché semántica: ¿ya respondida algo equivalente? → HIT
   ├─ router de complejidad: tier → modelo más barato suficiente
   └─ respuesta + log (inference table)
        │
        ├─ job asíncrono: judge de calidad + verificación de routing
        ├─ experimentos A/B: métricas por variante → ganador estadístico
        └─ monitor: P10 calidad < umbral → ROLLBACK automático + alerta
        │
   dashboards: ahorro €, calidad, hit rate, estado de experimentos
```

## Fases

### Semana 1 — Servicio base con caché y routing

- [ ] Corpus de 300+ consultas sintéticas de soporte (billing, técnico, cuenta, general; variadas en dificultad e idioma).
- [ ] Endpoint pyfunc: clasificación + resumen + sugerencia de respuesta.
- [ ] Caché semántica delante (umbral 0.95, TTL por tipo, invalidación por system prompt).
- [ ] Router de complejidad con clasificador propio + mapa tier→modelo en tabla Delta.

### Semana 2 — Experimentación

- [ ] Prompts versionados en MLflow Prompt Registry (UC).
- [ ] Motor de experimentos: split consistente por hash, métricas asíncronas (judge, accuracy vs golden), t-test/Mann-Whitney, auto-declaración de ganador.
- [ ] Golden set de 80+ consultas con clasificación y respuesta ideales (manual).

### Semana 3 — Rollout gobernado

- [ ] Feature flags de IA: plan de etapas 1%→5%→25%→50%→100% con canary estadístico.
- [ ] Modo shadow para variantes nuevas.
- [ ] Rollback automático por P10 de calidad + cooldown anti-flapping + alertas.

### Semana 4 — Operación y pulido

- [ ] Load test 2.000+ consultas (mezcla de únicas/repetidas) → informe de ahorro y latencias.
- [ ] Dashboard único de operación: ahorro € acumulado, hit rate de caché, calidad por variante, estado del rollout.
- [ ] Demo: variante mala → shadow la detecta → variante buena escala a 100% sola.

## Criterios de éxito

- [ ] Coste por consulta < 50% del coste "todo al modelo grande" con paridad de calidad medida.
- [ ] Una regresión de calidad se auto-retira antes del 25% de tráfico.
- [ ] Todo cambio de prompt pasa por experimento con estadística, jamás "a pelo".

## Datos y URLs

- Consultas sintéticas propias. Docs: https://docs.databricks.com/
- Contexto: *State of AI Agents* — soporte al cliente como top caso de uso; multi-modelo como norma (78% de empresas).

## ⚠️ Seguridad

- Consultas sintéticas; la caché y los logs solo con datos ficticios.
