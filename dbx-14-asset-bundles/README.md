# Ejercicio 14 — Databricks Asset Bundles (DABs) + Git

## Objetivo
Empaquetar un proyecto completo como código (IaC) y desplegarlo con Databricks Asset Bundles, versionado en Git.

## Conceptos
- Bundle = definición declarativa de jobs, pipelines, dashboards... en YAML
- Comandos: `databricks bundle validate | deploy | run | destroy`
- Targets: dev / prod con variables y overrides

## Tareas

### 1. Estructura del bundle
- [ ] `databricks bundle init` (template default-python o vacío)
- [ ] Entender: `databricks.yml`, `resources/`, `src/`
- [ ] Definir variables (catalog, env) y targets `dev` y `prod`

### 2. Definir recursos
- [ ] Un job que ejecute los notebooks del ETL Zenviro (raw→gold)
- [ ] Referenciar un pipeline SDP como recurso

### 3. Despliegue
- [ ] `databricks bundle validate -t dev`
- [ ] `databricks bundle deploy -t dev`
- [ ] `databricks bundle run -t dev <job>`
- [ ] Revisar en la UI los recursos creados con prefijo `[dev <usuario>]`

### 4. Subir a Git
- [ ] Añadir `.gitignore` (`.databricks/`, `.venv/`, tokens)
- [ ] Commit de la estructura del bundle
- [ ] Teoría: cómo sería el despliegue a `prod` desde CI/CD (GitHub Actions con service principal)

## Entregable
Bundle completo en `bundle/` + `docs/cicd.md` con el pipeline de GitHub Actions propuesto.
