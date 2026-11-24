# SOLUCIÓN — AN-02 (léela DESPUÉS de intentarlo)

## Parte A — Simulador (esqueleto)

```python
import numpy as np, pandas as pd, datetime as dt

rng = np.random.default_rng(42)          # semilla fija = reproducible
N = 50_000
inicio = dt.date(2025, 1, 6)

usuarios = pd.DataFrame({
    "user_id": range(N),
    "fecha_registro": [inicio + dt.timedelta(days=int(d)) for d in rng.integers(0, 84, N)],
    "pais": rng.choice(["ES", "MX", "AR", "CO"], N, p=[.5, .2, .15, .15]),
    "dispositivo": rng.choice(["ios", "android", "web"], N, p=[.35, .45, .2]),
    "variante": rng.choice(["A", "B"], N),                 # split 50/50
})

p_base = 0.12
p = np.where(usuarios["variante"] == "B", p_base + 0.02, p_base)
usuarios["convirtio"] = rng.binomial(1, p)

spark.createDataFrame(usuarios).write.mode("overwrite").saveAsTable("ab.usuarios")
```

Para `ab.eventos`: para cada usuario genera una Bernoulli diaria de actividad durante 28 días, con probabilidad mayor si `convirtio==1` (p. ej. 0,35 vs 0,15). Vectorízalo con numpy (no bucles por usuario sobre Spark) y guarda solo los días con actividad.

## Parte B — z-test y SRM

```python
from statsmodels.stats.proportion import proportions_ztest, proportion_confint
from scipy.stats import chisquare

pdf = spark.table("ab.usuarios").toPandas()
conv = pdf.groupby("variante")["convirtio"].agg(["sum", "count"])

# SRM: ¿el split es 50/50?
chi2, p_srm = chisquare(conv["count"].values)
# p_srm < 0.01 => SRM: el experimento NO es válido aunque el z-test salga

z, p_val = proportions_ztest(conv["sum"].values, conv["count"].values)
lo, hi = proportion_confint(conv["sum"], conv["count"], alpha=0.05, method="wilson")
```

**Lo que debe salir:** con 50k usuarios y +2 pp sobre 12%, el z-test da p ≪ 0,05 holgadamente. La calculadora de sample size pedirá ≈ 6.000–7.000 usuarios por rama: con 25k/rama vas sobrado.

**Errores comunes:**
- Hacer el test sobre eventos en vez de sobre usuarios (infla n y rompe la independencia: la unidad de análisis es el usuario).
- `peeking`: mirar el p-valor cada día y parar "cuando sale" multiplica los falsos positivos. Por eso se fija el n (o se usan tests secuenciales) ANTES de empezar.
- Declarar victoria con el lift relativo ("+16,7% de conversión") sin dar el absoluto (+2 pp): las dos cifras, siempre juntas.

**Respuestas modelo a las preguntas de entrevista:**
1. *Varianza disparada en B*: indica heterogeneidad (el efecto no es uniforme: quizá un segmento mejora y otro empeora). No lanzas global; segmentas antes. Si el efecto medio es positivo pero un segmento cae, lanzas solo a ese segmento o iteras.
2. *Parar en el día 3 con p=0,03*: no. Con miradas repetidas, la tasa de falsos positivos real puede pasar del 5% al 20–30%. Se espera al n prefijado.
3. *SRM*: cuando el reparto observado entre variantes se desvía del esperado más de lo que el azar permite (chi-cuadrado). Indica un bug de asignación o de telemetría; cualquier diferencia de conversión posterior es sospechosa porque los grupos ya no son comparables.

## Parte C — Triángulo de cohortes (la query)

```sql
WITH u AS (
  SELECT user_id, date_trunc('week', fecha_registro) AS cohorte
  FROM ab.usuarios
),
actividad AS (
  SELECT u.cohorte,
         floor(datediff(date_trunc('week', e.fecha_evento), u.cohorte) / 7) AS semana_n,
         COUNT(DISTINCT u.user_id) AS activos
  FROM u JOIN ab.eventos e USING (user_id)
  GROUP BY 1, 2
),
tam AS (SELECT cohorte, COUNT(*) AS n FROM u GROUP BY 1)
SELECT a.cohorte, a.semana_n,
       ROUND(100 * a.activos / t.n, 1) AS pct_retenidos
FROM actividad a JOIN tam t USING (cohorte)
ORDER BY 1, 2;
```

Pivota en pandas (`df.pivot(index="cohorte", columns="semana_n", values="pct_retenidos")`) y pinta `sns.heatmap(..., annot=True)`.

**Interpretación tipo:** la columna `semana_n=0` suele ser ~100% por construcción (se registraron = estuvieron activos); lo interesante es la pendiente de bajada y si se aplana. Una cohorte que retiene mejor merece la pregunta "¿qué pasó esa semana?" (campaña, release...).

## Qué decir en una entrevista

> "Simulé 50k usuarios con un efecto conocido de +2 pp, lo detecté con un z-test de proporciones verificando antes el SRM y la potencia con la calculadora de Evan Miller, y construí el triángulo de retención por cohortes semanales en una sola query de Spark SQL."
