# Ejercicio 14 — Databricks Asset Bundles (DABs) + Git

## Objetivo
Empaquetar un proyecto completo como código (IaC) y desplegarlo con Databricks Asset Bundles, versionado en Git.


## Por qué importa

Databricks Asset Bundles empaqueta todo tu proyecto (jobs, pipelines, dashboards) como código desplegable: la segunda mitad del dominio CI/CD (**10%**). Con bundles + GitHub Actions tienes el flujo profesional completo: desarrollo en rama, despliegue a dev, tests, despliegue a prod — todo reproducible y revisable en PRs.

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

## Criterios de éxito

- [ ] `databricks.yml` con al menos un job desplegado a dos targets (dev/prod).
- [ ] GitHub Action que valida el bundle en cada PR.
- [ ] Variables por target explicadas (por qué dev y prod NO comparten catálogo).

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Develop on Databricks* → Asset Bundles)

## ⚠️ Seguridad

El `DATABRICKS_TOKEN` de la GitHub Action va en **GitHub Secrets** del repo, jamás en el YAML del workflow en claro. Revisa `git diff --cached` antes de cada push.

## Entregable
Bundle completo en `bundle/` + `docs/cicd.md` con el pipeline de GitHub Actions propuesto.