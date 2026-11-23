# Solución — Ejercicio 14

## 1. Estructura
```bash
databricks bundle init
```
```
├── databricks.yml
├── resources/
│   └── zenviro_job.yml
├── src/               # notebooks / pipeline code
└── .gitignore
```

`databricks.yml`:
```yaml
bundle:
  name: zenviro-etl

variables:
  catalog:
    default: zenviro

targets:
  dev:
    mode: development
    default: true
    workspace:
      host: https://adb-xxxxxxxx.azuredatabricks.net
  prod:
    mode: production
    workspace:
      host: https://adb-xxxxxxxx.azuredatabricks.net
    variables:
      catalog: zenviro_prod
```

`resources/zenviro_job.yml`:
```yaml
resources:
  jobs:
    zenviro_etl:
      name: zenviro-etl-${bundle.target}
      tasks:
        - task_key: bronze
          notebook_task:
            notebook_path: ../src/bronze.py
        - task_key: silver
          depends_on: [{ task_key: bronze }]
          notebook_task:
            notebook_path: ../src/silver.py
        - task_key: gold
          depends_on: [{ task_key: silver }]
          notebook_task:
            notebook_path: ../src/gold.py
```

## 3. Comandos
```bash
databricks bundle validate -t dev
databricks bundle deploy   -t dev
databricks bundle run      -t dev zenviro_etl
databricks bundle destroy  -t dev   # limpieza
```

## 4. CI/CD (GitHub Actions) — docs/cicd.md
```yaml
# .github/workflows/deploy.yml
name: deploy-prod
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    env:
      DATABRICKS_HOST: ${{ secrets.DATABRICKS_HOST }}
      DATABRICKS_CLIENT_ID: ${{ secrets.DATABRICKS_CLIENT_ID }}
      DATABRICKS_CLIENT_SECRET: ${{ secrets.DATABRICKS_CLIENT_SECRET }}
    steps:
      - uses: actions/checkout@v4
      - run: pipx install databricks-cli
      - run: databricks bundle validate -t prod
      - run: databricks bundle deploy -t prod
```
Los secretos viven en **GitHub Secrets** (service principal) — nunca en el YAML ni en tokens personales.

`.gitignore`:
```
.databricks/
.venv/
*.pyc
databrickscfg
```

## Puntos de examen
- Bundles = IaC oficial de Databricks: todo el proyecto reproducible.
- `mode: development` añade prefijos y pausa schedules para no chocar con prod.
- En CI se usa service principal; tokens personales solo en local.
