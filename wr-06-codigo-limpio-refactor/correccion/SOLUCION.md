# Solución — WR-06

## src/transforms.py
```python
"""Utilidades de transformación reutilizables para PySpark."""
from pyspark.sql import DataFrame, Column
from pyspark.sql import functions as F

def parse_fecha(col: str, formatos: list[str] | None = None) -> Column:
    """Parsea una columna de texto a fecha probando varios formatos.

    Args:
        col: nombre de la columna de entrada.
        formatos: lista de patrones java.text (defecto: ISO y europeos).

    Returns:
        Columna de tipo date con la primera coincidencia válida.
    """
    formatos = formatos or ["yyyy-MM-dd", "dd/MM/yyyy", "MM-dd-yyyy"]
    return F.coalesce(*[F.to_date(col, f) for f in formatos])

def perfil_nulos(df: DataFrame) -> DataFrame:
    """Devuelve el % de nulos por columna."""
    total = df.count()
    rows = [(c, df.filter(F.col(c).isNull()).count() / total)
            for c in df.columns]
    return df.sparkSession.createDataFrame(rows, ["columna", "pct_nulos"])
```

## Notebook refactorizado
```python
import sys
sys.path.append("/Workspace/Users/tu_usuario/Databricks-learn/wr-06-codigo-limpio-refactor/src")
from transforms import parse_fecha, perfil_nulos

df_limpio = (spark.table("main.raw.viajes")
    .withColumn("fecha", parse_fecha("fecha_txt")))
display(perfil_nulos(df_limpio))
```

## tests/test_transforms.py
```python
import pytest
from databricks.connect import DatabricksSession  # o spark local
from src.transforms import parse_fecha, perfil_nulos

@pytest.fixture(scope="session")
def spark():
    return DatabricksSession.builder.getOrCreate()

def test_parse_fecha_multi(spark):
    df = spark.createDataFrame([("2026-01-05",), ("05/01/2026",)], ["f"])
    out = df.select(parse_fecha("f").alias("d")).collect()
    assert all(r.d is not None for r in out)
    assert out[0].d == out[1].d

def test_perfil_nulos(spark):
    df = spark.createDataFrame([(1, None), (2, "a")], ["a", "b"])
    res = {r.columna: r.pct_nulos for r in perfil_nulos(df).collect()}
    assert res["b"] == 0.5 and res["a"] == 0.0
```

## Scope
```python
total = 0  # global
def suma(x):
    total_local = x  # local: muere al salir
    return x * 2
```
Mutar globales dentro de funciones oculta dependencias → bugs y tests imposibles. Pasa y devuelve valores.

## Puntos clave
- Regla de oro: 2+ usos → función; función compleja + 2+ notebooks → módulo compartido.
- Type hints + docstrings = documentación viva (`help()`).
- Notebooks para explorar, módulos + tests para producción.
