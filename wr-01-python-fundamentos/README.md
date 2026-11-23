# WR-01 — Fundamentos de Python en Databricks

> Basado en los capítulos 1-2 de *Practical Python Data Wrangling and Data Quality* (McGregor), trasladado a notebooks de Databricks.

## Objetivo
Dominar lo esencial de Python (variables, funciones, bucles, condicionales, errores) dentro de un notebook de Databricks, con un mini-caso de datos reales.

## Datos
- Citi Bike (datos reales de viajes): https://citibikenyc.com/system-data ✅ verificada en [docs/fuentes-de-datos.md](../../docs/fuentes-de-datos.md)

## Tareas

### 1. Hola mundo y variables
- [ ] `print("Hola Databricks")` en un notebook
- [ ] Variables: string, int, float, bool, list, dict — una de cada
- [ ] F-strings para formatear salida

### 2. Funciones
- [ ] Escribir una función `celsius_a_fahrenheit(c)` y probarla
- [ ] Escribir una función con parámetro por defecto y argumentos con nombre
- [ ] Importar y usar una función de una librería (`statistics.mean`)

### 3. Bucles y condicionales
- [ ] Recorrer una lista de distancias y clasificar cada viaje: `< 2 km` corto, `< 10 km` medio, resto largo
- [ ] Contar cuántos viajes de cada tipo hay (dict como contador)

### 4. Errores
- [ ] Provocar y corregir: un `SyntaxError`, un `TypeError` (sumar str + int), un `IndexError`
- [ ] Usar `try/except` para capturar una división entre cero

### 5. Mini-caso Citi Bike (pseudocódigo → código)
- [ ] Escribe en Markdown el pseudocódigo de: "leer duraciones de viajes, quedarte con las > 5 min, calcular la media"
- [ ] Implementa ese pseudocódigo con una lista de datos inventados

### 6. Versionado
- [ ] Conecta el notebook a este repo con Databricks Git folders y haz tu primer commit desde la UI

## Entregable
Notebook `notebooks/wr01_fundamentos.py` commiteado desde Databricks.
