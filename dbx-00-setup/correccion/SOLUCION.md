# Solución — Ejercicio 0

## 2. Cluster
Compute → Create compute:
- Single node, runtime LTS (p. ej. 15.x), terminate after 30 min.
- All-purpose = interactivo; Job cluster = solo para jobs (se apaga al terminar).

## 3. Comandos mágicos

```python
# Celda 1 (python)
print("Hola Databricks")
```

```sql
-- Celda 2: %sql
SELECT current_timestamp() AS ahora, current_user() AS usuario;
```

```bash
# Celda 3: %fs
%fs ls /
```

```bash
# Celda 4: %sh (se ejecuta en el driver, no es el DBFS)
%sh date
```

```markdown
Celda 5: %md
# Título en Markdown
```

## 4. DBFS y dbutils

```python
# Listar raíz de DBFS
display(dbutils.fs.ls("/"))

# Escribir y leer un fichero
dbutils.fs.put("/tmp/ej0_hola.txt", "Hola DBFS", True)
print(dbutils.fs.head("/tmp/ej0_hola.txt"))

# Widgets
dbutils.widgets.text("nombre", "sergio", "Tu nombre")
nombre = dbutils.widgets.get("nombre")
print(f"Hola {nombre}")
dbutils.widgets.removeAll()

# Secrets (solo listar)
display(dbutils.secrets.listScopes())
```

Equivalente `%fs`:
```bash
%fs ls /tmp
%fs head /tmp/ej0_hola.txt
```

## 5. Debug
```python
x = None
print(x.len)  # AttributeError -> abre el traceback y localiza la línea
```
En el notebook: Run → Debug cell, añade breakpoint en la línea del print.

## Puntos de examen
- DBFS root es el almacenamiento por defecto del workspace.
- `%sh` corre en el driver, no en los workers.
- Los widgets sirven para parametrizar notebooks y jobs.
