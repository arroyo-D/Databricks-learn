# Ruta de aprendizaje — de cero a portfolio completo

Esta es la ruta oficial del programa. Está diseñada para que **no te saltes nada**: cada paso usa lo que construiste en el anterior. Si un módulo te resulta imposible, la causa casi siempre es un prerrequisito no cubierto — vuelve a esta página y revisa qué te falta.

## Fase 0 — Requisitos previos (antes de escribir una línea)

| # | Requisito | Cómo conseguirlo | ¿Obligatorio? |
|---|---|---|---|
| 0.1 | **Cuenta de Databricks** | Databricks Free Edition (gratis, sin tarjeta) o trial de 14 días en Azure/AWS/GCP. Desde https://docs.databricks.com/ → *Sign up for Databricks for free* | ✅ Sí, todo se hace aquí |
| 0.2 | **GitHub** | Ya lo tienes (`arroyo-D`). Sirve para versionar notebooks con Git folders y para los ejercicios de CI/CD | ✅ Sí |
| 0.3 | **Curso PDF** (Udatademy Data Engineer Associate) | Comprado. Es la lectura teórica del track `dbx`: cada módulo indica qué capítulos leer ANTES de las tareas | ✅ Sí |
| 0.4 | **Libro McGregor** (*Practical Python Data Wrangling*) | Lo tienes. Teoría del track `wr` | ✅ Sí (salvo que domines Python) |
| 0.5 | **Libro Maffeo** (*Designing Data Governance*) | Lo tienes. Teoría del track `gov` | ✅ Sí |
| 0.6 | **Guías BASWE** (15 AI Projects + AI Pivot Field Guide) | Las tienes. Contexto del track `ai` | ⬜ Solo lectura de apoyo |
| 0.7 | **Python básico** | Si no dominas Python, el track `wr` te lo da empezando por `wr-01`. Si lo dominas, haz `wr-01` a modo de repaso rápido | ⚠️ Valora tu nivel |

> ⚠️ **Regla de oro de seguridad**: desde el primer día, cualquier credencial (token de Databricks, API keys) va en Databricks Secrets o variables de entorno — nunca en código. Este repo es público.

## ¿Te atascas? Orden de recursos (sin pedir ayuda externa)

1. **Relee el README del módulo** — la sección *Por qué importa* suele responder al "¿para qué?" y los *Recursos* apuntan a la sección exacta de la documentación.
2. **Databricks Assistant** — el asistente de IA integrado en el propio workspace: escríbele en la celda qué quieres hacer o pégale el error; genera y corrige código en contexto.
3. **Documentación oficial** — https://docs.databricks.com/ (usa el buscador; es la fuente que siempre está al día cuando la UI cambia).
4. **`correccion/SOLUCION.md`** — léela entera: explica el porqué de cada decisión y los errores comunes (probablemente el tuyo está ahí).
5. **Preguntas del módulo** (track dbx) — si fallas preguntas de un tema, ese tema no está dominado: repásalo antes de seguir.
6. **Bitácora** — anota el atasco: medio se resuelve al escribirlo, y si no, queda registrado para repasar.

Si nada de eso lo desbloquea, déjalo anotado en la bitácora y continúa con el siguiente módulo si no es prerrequisito: muchos conceptos "encajan" al verlos en uso un módulo después.

## Fase 1 — Fundamentos y certificación (track `dbx`, en orden estricto)

Los módulos `dbx` son **secuenciales**: cada uno asume el anterior. No los reordenes.

```
dbx-00 setup ──► dbx-01 Unity Catalog ──► dbx-02 ingesta ──► dbx-03 transformaciones
     │                                                        │
dbx-04 PySpark ◄──────────────────────────────────────────────┘
     │
     ├──► dbx-05 streaming ──► dbx-06 Auto Loader
     │                                 │
     ├──► dbx-07 Delta Lake ◄──────────┘
     │           │
     └──► dbx-08 Lakeflow Jobs ──► dbx-09 Lakeflow SDP ──► dbx-10 Databricks SQL
                                         │
dbx-11 gobernanza ──► dbx-12 sharing/federation
     │
     └──► dbx-13 Connect ──► dbx-14 Asset Bundles (CI/CD)
```

- **Cómo trabajar cada módulo**: 1) lee el capítulo del PDF indicado, 2) lee el README completo (incluido *Por qué importa*), 3) haz las tareas SIN mirar la solución, 4) comprueba contra los *Criterios de éxito*, 5) solo entonces contrasta con `correccion/SOLUCION.md`, 6) haz las preguntas de `preguntas/PREGUNTAS.md` — son tu termómetro para el examen.
- **Hito**: al terminar `dbx-14` puedes presentarte al examen **Databricks Certified Data Engineer Associate** (45 preguntas, 90 min, 70% para aprobar).

## Fase 2 — Python y calidad de datos (track `wr`, en paralelo al track dbx)

El track `wr` no depende del `dbx` más allá de `dbx-00` (necesitas saber manejar un notebook). Puedes intercalarlo: por ejemplo, 2 días dbx + 1 día wr por semana.

```
wr-01 Python ──► wr-02 calidad (conceptos) ──► wr-03 formatos ──► wr-04 APIs/scraping
                                                        │
wr-05 limpieza ◄────────────────────────────────────────┘
     │
     ├──► wr-06 código limpio ──► wr-07 EDA ──► wr-08 visualización
```

- Si ya dominas Python: `wr-01` en modo repaso y empieza fuerte en `wr-02`.
- `wr-03` y `wr-04` refuerzan directamente `dbx-02` y `dbx-06` (ingesta): ideal hacerlos por la misma época.

## Fase 3 — Gobernanza (track `gov`, después de dbx-11)

```
gov-01 framework ──► gov-02 roles ──► gov-03 consejo/diccionario
        │
        └──► gov-04 roadmap ──► gov-05 desarrollo gobernado ──► gov-06 monitorización
```

- **Prerrequisito**: `dbx-01` y `dbx-11` (Unity Catalog). Sin ellos, los ejercicios de gobernanza no tienen dónde aterrizar.
- Momento ideal: justo después de `dbx-11`/`dbx-12`, cuando la parte técnica de permisos está fresca.

## Fase 4 — Proyectos de analista (track `an`, tras wr-05/wr-07)

**Prerrequisito**: `dbx-03` (transformaciones) + `wr-04`, `wr-05` y `wr-07`. No depende del track `gov` ni del `ai`.

```
an-01 precios/datos públicos ──► an-02 A/B + cohortes ──► an-03 texto/LLM ──► an-04 warehouse + app
```

- ⏰ **Empieza `an-01` (Parte A) lo antes posible**: el tracker de precios necesita 30 días de captura real. Prográmalo el primer día y sigue con otros módulos mientras el job trabaja.
- `an-03` y `an-04` también tienen jobs periódicos (semanal/diario): cuando llegues a ellos ya tendrás el hábito.
- Adaptado a España: datos.gob.es como fuente principal de datos públicos.

## Fase 5 — AI Engineering (track `ai`, tras dominar la base)

**Prerrequisito duro**: `dbx-00` a `dbx-10` completos + `ai-01` como puerta de entrada.

Orden recomendado (el README maestro lo detalla):

1. **Base de evaluación**: `ai-01` → `ai-02` → `ai-07`
2. **Recuperación y generación**: `ai-06` → `ai-08` → `ai-04`
3. **Producción seria**: `ai-03` → `ai-09` → `ai-11` → `ai-12` → `ai-13`
4. **Cumbres**: `ai-10` → `ai-14` → `ai-15`

Cada README del track ai indica sus **prerrequisitos exactos** en la cabecera. No hacen falta los 15: elige 2–3 para construir de verdad (los capstones los integran).

## Fase 6 — Machine Learning con datos NASA (track `ml`, nivel avanzado)

**Prerrequisito**: `wr-05`, `wr-07` + `dbx-03`. Recomendado: `ai-01` (cultura de evaluación). Puede hacerse en paralelo al track `ai`.

```
ml-01 series NASA (anomalías + exoplanetas, CPU) ──► ml-02 segmentación Marte (GPU)
```

- `ml-02` necesita GPU: cluster GPU de Databricks (de pago) o Colab/Kaggle gratis con registro en MLflow de Databricks — el módulo explica ambas rutas.
- Los dos son piezas de portfolio con datasets reales de la NASA: baselines obligatorios, control de leakage y métricas honestas.

## Fase 7 — Proyectos integradores (track `cap`, el examen final no oficial)

```
cap-01 (plataforma E2E)      ← requiere dbx-01…dbx-10
cap-02 (knowledge assistant) ← requiere ai-06, ai-01, ai-13
cap-03 (fábrica documentos)  ← requiere ai-14, ai-03
cap-04 (soporte gobernado)   ← requiere ai-02, ai-07, ai-09, ai-12
cap-05 (multiagente)         ← requiere ai-15, ai-05, ai-11
cap-06 (smart city + Kafka)  ← requiere dbx-05…10; cap-01 recomendado
```

- Empieza por `cap-01` siempre: es el que consolida toda la ingeniería de datos.
- `cap-06` es la opción si tienes un servidor donde correr Kafka (o quieres aprender streaming real con Docker en local); su alternativa sin servidor está incluida en el propio módulo.
- Los demás elígelos según el rol que busques (cada uno mapea a un caso de uso top del *State of AI Agents*).

## Checklist global de "no me he saltado nada"

- [ ] Fase 0: cuenta creada, cluster arrancado, primer notebook ejecutado
- [ ] dbx-00 → dbx-14 con sus preguntas de examen hechas
- [ ] wr-01 → wr-08 (o solo wr-02 → wr-08 si dominas Python)
- [ ] gov-01 → gov-06 tras dbx-11/12
- [ ] an-01 → an-04 (con el tracker de precios corriendo desde el primer día)
- [ ] 2–3 módulos del track ai construidos de verdad
- [ ] ml-01 (y ml-02 si tienes GPU o usas Colab/Kaggle)
- [ ] cap-01 + 1–2 capstones más
- [ ] Examen de certificación superado (o simulacros >80% en las preguntas de los módulos dbx)

Si alguna casilla no se puede marcar, en esa cadena de dependencias está la respuesta de por dónde seguir.
