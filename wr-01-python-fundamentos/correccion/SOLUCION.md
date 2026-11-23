# Solución — WR-01

## 1-2. Variables y funciones
```python
nombre = "Ana"          # str
edad = 30               # int
altura = 1.68           # float
activo = True           # bool
skills = ["sql", "python"]        # list
perfil = {"rol": "DE", "nivel": 1}  # dict
print(f"{nombre} tiene {edad} años")

def celsius_a_fahrenheit(c: float) -> float:
    return c * 9/5 + 32

def saludar(nombre, saludo="Hola"):
    return f"{saludo}, {nombre}"
saludar(nombre="Ana")

from statistics import mean
mean([1, 2, 3, 4])
```

## 3. Bucles y condicionales
```python
distancias = [1.2, 5.5, 12.0, 0.8, 3.3]
contador = {"corto": 0, "medio": 0, "largo": 0}
for d in distancias:
    if d < 2:
        contador["corto"] += 1
    elif d < 10:
        contador["medio"] += 1
    else:
        contador["largo"] += 1
print(contador)  # {'corto': 2, 'medio': 2, 'largo': 1}
```

## 4. Errores
```python
try:
    10 / 0
except ZeroDivisionError as e:
    print(f"Error capturado: {e}")
```
- SyntaxError: código mal escrito (falla al interpretar).
- TypeError: tipos incompatibles (`"2" + 2`).
- IndexError: `lista[10]` en lista de 3.

## 5. Pseudocódigo → código
```
# PSEUDOCÓDIGO
# duraciones <- lista de minutos
# largos <- duraciones donde valor > 5
# imprimir media(largos)

duraciones = [3, 12, 7, 25, 2, 9]
largos = [d for d in duraciones if d > 5]
print(mean(largos))  # 13.25
```

## 6. Git desde Databricks
Workspace → Git folders → Add Git folder → URL de este repo → Create. Después: icono Git del notebook → Commit & push.

## Puntos clave
- Listas y dicts cubren el 80% del wrangling básico.
- Pseudocódigo primero, código después (ahorra horas).
- `try/except` para errores esperables; no para ocultar bugs.
