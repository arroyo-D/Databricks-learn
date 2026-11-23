# Solución de referencia — ai-05 · Arbitraje de salidas LLM

> ⚠️ Intenta el ejercicio antes de leer esto.

## Arquitectura de referencia

```
              ┌──► Critic A (Llama-70B, FMAPI) ──────┐
salida LLM ──►├──► Critic B (Claude, AI Gateway) ────┼──► disagreement detector
              └──► Critic C (Llama-8B, FMAPI) ───────┘          │
                          (LangGraph fan-out paralelo)           ▼
                                                      Adjudicador (Llama-70B)
                                                              │
                                          veredicto ──► arbitrations (Delta)
                                                              │
                                                   dashboard + meta-analítica
```

## Código clave

### Crítica estructurada

```python
from pydantic import BaseModel

class Issue(BaseModel):
    quote: str; problem: str; severity: int  # 1-5

class Critique(BaseModel):
    dimension: str; score: int; issues: list[Issue]; self_confidence: int

CRITIC_PROMPT = """Eres el crítico de {dimension}. Evalúa esta salida LLM.
Pregunta original: {question}\nSalida: {output}
Devuelve JSON: {schema}"""

def run_critic(model, dimension, question, output) -> Critique:
    raw = ai_query(model, CRITIC_PROMPT.format(...), response_format=json_schema)
    return Critique.model_validate_json(raw)
```

### Grafo LangGraph (esqueleto)

```python
from langgraph.graph import StateGraph

g = StateGraph(ArbState)
g.add_node("critics", parallel_critics)      # asyncio.gather de los 3
g.add_node("disagreements", detect_disagreements)
g.add_node("adjudicate", adjudicator)
g.add_edge("__start__", "critics")
g.add_conditional_edges("critics",
    lambda s: "adjudicate" if not all_agree_perfect(s) else "__end__")
g.add_edge("disagreements", "adjudicate")
```

### Detector de desacuerdos

```python
def detect_disagreements(critiques):
    flags = []
    for a, b in itertools.combinations(critiques, 2):
        if abs(a.score - b.score) >= 2:
            flags.append({"type": "score_gap", "between": [a.dimension, b.dimension]})
    # + issues vistos por un solo crítico
    return flags
```

### Persistencia y meta-analítica

```sql
CREATE TABLE main.ai_arb.arbitrations (
  arb_id STRING, question STRING, output STRING,
  critiques VARIANT, disagreements ARRAY<STRING>,
  verdict VARIANT, latency_ms INT, cost_usd DOUBLE, ts TIMESTAMP);

-- ¿Qué crítico es más revocado?
SELECT c.dimension,
       count_if(v.dismissed_count > 0) * 1.0 / count(*) AS overrule_rate
FROM main.ai_arb.arbitrations
LATERAL VIEW explode(critiques) t AS c
LATERAL VIEW explode(verdict) t2 AS v GROUP BY 1;
```

## Decisiones clave

- **Modelos distintos por crítico**: tres críticos con el mismo modelo comparten los mismos puntos ciegos; la diversidad es el producto.
- **Cortocircuito en acuerdo total**: si los 3 dicen 5/5 sin issues, no pagues al adjudicador.
- **Veredicto con flags descartados**: explicar por qué se rechaza una crítica es tan valioso como confirmarla (auditable).

## Errores comunes

- Fan-out secuencial: triplica latencia sin ganar nada.
- Dejar que el adjudicador ignore críticas sin razonarlo: obliga a justificar cada desacuerdo.
- No guardar latencia/coste por llamada: sin eso no hay meta-analítica seria.

## Cómo validarlo

1. Salida con 2 errores factuales plantados → veredicto los confirma con citas.
2. Salida excelente → cortocircuito sin adjudicador, score alto.
3. Tira un endpoint (mock 500) → veredicto degradado con nota de confianza reducida.
