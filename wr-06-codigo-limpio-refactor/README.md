# WR-06 — Código Limpio: Funciones, Scope y Refactorización

> Capítulo 8 de McGregor aplicado a código de datos en Databricks: cuándo crear funciones, scope, docstrings, y notebooks vs scripts.

## Objetivo
Convertir notebooks "espagueti" en código reutilizable, testeable y documentado.

## Tareas

### 1. Cuándo crear una función
- [ ] Revisa tu notebook de WR-05: identifica código repetido 2+ veces y extráelo a funciones
- [ ] Criterio del libro: ¿lo usaré más de una vez? ¿es feo/confuso? ¿odio el comportamiento por defecto?

### 2. Funciones bien hechas
- [ ] Escribe `parse_fecha(col, formatos)` con docstring, type hints y valor por defecto
- [ ] Escribe `perfil_nulos(df)` que devuelva un DataFrame con % de nulos por columna
- [ ] Parámetros obligatorios vs opcionales; `*args`/`**kwargs` en un ejemplo

### 3. Scope
- [ ] Demuestra con un ejemplo la diferencia entre variable local y global
- [ ] Explica por qué mutar una global desde una función es mala idea

### 4. Refactor real
- [ ] Coge una celda larga de WR-05 y refractórala en 3 funciones con nombres claros
- [ ] Comenta la legibilidad antes/después en una celda Markdown

### 5. De notebook a módulo
- [ ] Mueve tus funciones a `src/transforms.py`
- [ ] Impórtalas en el notebook (Git folder + `sys.path` / import de workspace files)
- [ ] Escribe 3 tests con `pytest` para `parse_fecha` y `perfil_nulos`

### 6. Documentación
- [ ] Docstrings estilo Google en todas las funciones
- [ ] Genera ayuda con `help(tu_funcion)` en una celda

## Entregable
`src/transforms.py` + `tests/test_transforms.py` + notebook refactorizado.
