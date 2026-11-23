# Entorno local — qué instalar y cuándo lo necesitas

La buena noticia: **el 90% del programa no requiere instalar nada**. Todo se hace en el navegador dentro del workspace de Databricks.

## Qué NO necesita instalación (solo navegador)

- Todo el track `dbx-00` → `dbx-12`
- Todo el track `wr` y `gov`
- La mayoría del track `ai` y de los capstones (notebooks, jobs, pipelines, dashboards, serving)

## Qué SÍ necesita entorno local

| Módulo | Por qué |
|---|---|
| `dbx-13-databricks-connect` | Desarrollo desde tu IDE contra el cluster remoto |
| `dbx-14-asset-bundles` | Bundles se editan y despliegan desde local + GitHub Actions |
| `ai-04-docs-autocurables` | Scripts que corren en GitHub Actions / local |
| Cualquier capstone si quieres workflow profesional | Tests locales (pytest), pre-commit |

## Instalación (macOS)

```bash
# 1. Homebrew (si no lo tienes): https://brew.sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2. Git (probablemente ya lo tienes)
brew install git

# 3. Python 3.11+ y gestor de entornos
brew install python@3.12 uv

# 4. Databricks CLI (herramienta oficial)
brew install databricks

# 5. VS Code + extensión oficial de Databricks (recomendado)
brew install --cask visual-studio-code
# dentro de VS Code: buscar e instalar la extensión "Databricks" (de Databricks)
```

## Configuración (5 minutos)

```bash
# Autenticación por perfil — te pedirá host y token
databricks configure --token
#   host: https://adb-xxxxxxxx.azuredatabricks.net  (el de TU workspace)
#   token: dapi...  (se genera en Databricks: User Settings → Developer → Access tokens)

# Verificación: debe listar tus clusters
databricks clusters list
```

> ## ⚠️ Seguridad
>
> - El token se guarda en `~/.databrickscfg`, que está en el `.gitignore` de este repo. **Nunca** lo copies a un notebook, script o YAML.
> - Dale al token la caducidad mínima razonable (p. ej. 90 días) y revócalo si sospechas que se expuso.
> - En GitHub Actions, el token va en **Settings → Secrets → `DATABRICKS_TOKEN`**, jamás en el workflow en claro.

## Comprobación final

- [ ] `databricks clusters list` devuelve tu cluster
- [ ] VS Code abre una carpeta conectada al workspace (extensión Databricks, icono en la barra lateral)
- [ ] `python -c "import databricks.connect; print('ok')"` en un entorno `uv` con `databricks-connect` instalado

Cuando estas tres casillas estén en verde, `dbx-13` y `dbx-14` se hacen sin fricción. Hasta entonces, no necesitas nada de esto.
