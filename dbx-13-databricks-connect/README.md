# Ejercicio 13 — Databricks Connect (desarrollo local)

## Objetivo
Ejecutar código Spark desde tu máquina local (VS Code) contra el cluster de Databricks.


## Por qué importa

Databricks Connect te permite desarrollar en tu IDE local (VS Code, PyCharm) ejecutando contra el cluster remoto: el flujo de trabajo profesional real. Es la primera mitad del dominio CI/CD (**10%**): código en Git, tests locales, ejecución remota. Además es la base para los proyectos AI que corren fuera de notebooks.

## Conceptos
- Databricks Connect = cliente ligero: tu código corre local, el cómputo en el cluster remoto
- Ideal para desarrollo con IDE, tests unitarios y debugging local

## Tareas

### 1. Configuración del entorno local
- [ ] Instalar Python + entorno virtual
- [ ] `pip install databricks-connect==<versión-runtime-cluster>`
- [ ] Crear un **Personal Access Token** en Databricks (User Settings → Developer)
- [ ] Configurar `~/.databrickscfg` o variables de entorno:
  `DATABRICKS_HOST`, `DATABRICKS_TOKEN`, `DATABRICKS_CLUSTER_ID`
- [ ] Validar con `databricks-connect test` (o ejecutando un spark.range)

### 2. Desarrollo local
- [ ] Script `src/consulta_gold.py`: leer `zenviro.gold.*` y mostrar resultados
- [ ] Ejecutar desde VS Code y comprobar que el trabajo aparece en el cluster remoto

### 3. Tests
- [ ] Escribir un test con pytest sobre una función de transformación (p. ej. normalización de nombres del ejercicio 3)

### 4. Restricciones (teoría)
- [ ] Lista en `docs/limitaciones.md` qué NO se puede hacer con Connect (dbutils parcial, R, display...)

## ⚠️ Seguridad
El token va en `~/.databrickscfg` (local, en `.gitignore`) — **nunca** en el código ni en Git.

## Criterios de éxito

- [ ] Script local ejecutando contra el cluster remoto vía Connect.
- [ ] Tests unitarios de una función de transformación corriendo en local (pytest).
- [ ] Entorno reproducible: requirements/uv + README de setup.

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Develop on Databricks* → Databricks Connect)

## ⚠️ Seguridad

El token de Databricks vive en `~/.databrickscfg` (que está en el `.gitignore`) o en variables de entorno. **Nunca** en el código ni en notebooks commiteados. Si sospechas que un token se filtró, revócalo y genera otro.

## Entregable
Código en `src/`, tests en `tests/`, `requirements.txt` y notas de configuración.