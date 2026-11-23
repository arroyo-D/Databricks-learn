# Solución — Ejercicio 13

## 1. Setup
```bash
python -m venv .venv && source .venv/bin/activate
pip install "databricks-connect==15.4.*"   # misma versión mayor.menor que el runtime del cluster
```

`~/.databrickscfg` (⚠️ archivo LOCAL, cubierto por .gitignore — nunca subir a Git):
```ini
[DEFAULT]
host       = https://adb-xxxxxxxx.azuredatabricks.net
token      = dapiXXXXXXXXXXXXXXXX   # placeholder: tu token real solo en local
cluster_id = 1234-567890-abcdef
```

Validación:
```bash
databricks-connect test
```

## 2. Script local
```python
# src/consulta_gold.py
from databricks.connect import DatabricksSession

spark = DatabricksSession.builder.getOrCreate()

df = spark.table("zenviro.gold.gasto_por_ubicacion")
df.orderBy(df.gasto_salarial.desc()).show(10)
spark.stop()
```
El plan se ejecuta en el cluster remoto; el resultado vuelve a local.

## 3. Test con pytest
```python
# tests/test_transform.py
import pytest
from databricks.connect import DatabricksSession
from pyspark.sql import functions as F

@pytest.fixture(scope="session")
def spark():
    return DatabricksSession.builder.getOrCreate()

def test_norm_name(spark):
    df = spark.createDataFrame([("  ana ",)], ["name"])
    out = df.select(F.upper(F.trim("name")).alias("n")).first().n
    assert out == "ANA"
```

## 4. Limitaciones típicas
- No hay `display()` de notebook (usa `.show()`/`.collect()`)
- `dbutils` solo parcial (widgets no aplican igual)
- No soporta R / Scala SQL en local
- Requiere cluster encendido y runtime compatible

## Puntos de examen
- La versión de `databricks-connect` debe casar con el Databricks Runtime del cluster.
- Connect usa gRPC; el driver sigue siendo remoto.
- PAT en `~/.databrickscfg` o variables de entorno — nunca en el código ni en Git.
