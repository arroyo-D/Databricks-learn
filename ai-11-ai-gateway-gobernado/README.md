# ai-11 · Gateway de LLMs gobernado (rate limits, fallback, observabilidad)

> Serie **AI Engineering sobre Databricks** — adaptación del *Proyecto 11: LLM Gateway* de la guía BASWE.

**Nivel:** 🔴 Avanzado · **Duración estimada:** 12–14 días (2–3 h/día)
**Prerrequisitos:** `ai-02`, `dbx-11-gobernanza`

## Qué vas a construir

Un gateway de APIs delante de todos los LLMs de una organización ficticia: límites de uso y presupuesto **por equipo**, fallback automático a proveedores alternativos cuando el primario falla, y observabilidad unificada de cada interacción. Databricks AI Gateway hace gran parte del trabajo nativo — tu trabajo es configurarlo, extenderlo y demostrarlo con datos.

## De la guía original a Databricks

| Guía original | En Databricks |
|---|---|
| Gateway FastAPI/Go custom | **Mosaic AI Gateway** sobre serving endpoints (nativo) |
| Rate limiting con Redis | **Rate limits de AI Gateway** por endpoint/usuario |
| Presupuestos por equipo | Usage tracking de AI Gateway + `system.billing.usage` + alertas |
| Fallback entre proveedores | External Models (OpenAI, Anthropic) configurados + lógica de fallback en pyfunc |
| OpenTelemetry + Prometheus | **Inference tables** (request/response logging) + AI/BI Dashboards + SQL Alerts |
| Guardrails de contenido | **AI Guardrails** nativos (safety filters, PII detection) |

## Fases del proyecto

### Fase 1 — Proxy unificado (días 1–3)

- [ ] Crea serving endpoints para 2–3 modelos: uno de Foundation Model APIs y al menos un External Model (OpenAI o Anthropic) vía AI Gateway.
- [ ] Formato estándar de request/response independiente del proveedor.
- [ ] Activa **AI Gateway** en los endpoints: usage tracking + inference tables activadas (cada request/response aterriza en Delta automáticamente).
- [ ] Equipos ficticios: 3 service principals (data-eng, marketing, producto) con permisos distintos por endpoint.

### Fase 2 — Límites y presupuestos (días 3–6)

- [ ] Rate limits por equipo: configura límites distintos en AI Gateway (requests/minuto) y demuestra el 429 con cliente real.
- [ ] Presupuestos: query sobre `system.billing.usage` y las inference tables que calcula gasto por equipo/día; alerta al 80%, bloqueo lógico al 100% (tabla de config de presupuestos + job guardián).
- [ ] Prioridades: tráfico batch (etiquetado) vs tiempo real; documenta cómo priorizarías (colas, endpoints separados).
- [ ] API/SQL de administración: estado de límites por equipo, ajuste sin redeploy (tabla de config), log de cambios administrativos.

### Fase 3 — Fallback y resiliencia (días 6–9)

- [ ] Health checking: job que envía requests ligeros cada 30s a cada endpoint y registra estado (healthy/degraded/down) en ventanas rodantes.
- [ ] Fallback automático: pyfunc router que intenta el primario con retry + backoff exponencial (máx 3) y, si falla por error retryable, salta al siguiente de la cadena de fallback **por tier** (no por modelo exacto).
- [ ] Circuit breaker: N fallos en M segundos → circuito abierto (todo al fallback); tras cooldown, half-open con una request de prueba; éxito → cerrar. Log de cada cambio de estado.
- [ ] Distingue errores retryables (429, timeouts) de no-retryables (401, content policy): solo los primeros disparan retry/fallback.

### Fase 4 — Observabilidad (días 9–11)

- [ ] Inference tables como fuente: requests/s por equipo/modelo/proveedor, error rate por tipo, P50/P95/P99, tokens/s, coste por equipo/día, tasa de fallback, cambios de circuit breaker.
- [ ] 3 dashboards: Operaciones (salud de proveedores, fallbacks, circuitos), Negocio (gasto por equipo vs presupuesto, tendencias), Rendimiento (latencias, throughput).
- [ ] Alertas SQL: error rate > umbral, equipo al 80% de presupuesto, P99 > SLA, circuito abierto → webhook con contexto accionable.

### Fase 5 — Tests y load test (días 11–13)

- [ ] Suite de integración: rate limit bajo concurrencia, presupuesto bloquea en el umbral exacto, fallback se activa con proveedor caído (simulado), circuit breaker abre y cierra, streaming intacto.
- [ ] Load test: 5.000+ requests concurrentes mezclando equipos/modelos. Mide: overhead del router, precisión del rate limiting, comportamiento con outage simulado, fidelidad de los dashboards.

### Fase 6 — Pulido (días 13–14)

- [ ] Demo ≤4 min: tráfico fluyendo con métricas en vivo, outage simulado → fallback, rate limit mordiendo a un equipo, circuit breaker abriendo y recuperando.
- [ ] Narrativa de infraestructura: "gateway con failover multi-proveedor, enforcement de presupuesto por equipo y overhead <10ms".

## Criterios de éxito

- [ ] Tumbar el endpoint primario no produce errores visibles para el cliente (fallback transparente).
- [ ] Un equipo que se pasa de presupuesto queda bloqueado y alertado.
- [ ] Cada request deja rastro consultable en las inference tables.

## Datos y URLs

- Tráfico sintético generado por ti (mezcla de prompts de `ai-02`).
- Documentación: [docs.databricks.com](https://docs.databricks.com/) (*AI and machine learning* → AI Gateway; *Administration* → system tables).

## ⚠️ Seguridad

- Las API keys de OpenAI/Anthropic solo en la configuración de External Models de AI Gateway.
- Las inference tables contienen prompts y respuestas: controla su acceso con UC y no las exportes al repo.
