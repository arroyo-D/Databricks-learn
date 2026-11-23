# Preguntas de práctica — dbx-14 / Dominio: Implementing CI/CD (10%)

**P1.** Un Databricks Asset Bundle es:
- A) Un archivo zip de notebooks
- B) Definición declarativa en YAML de los recursos del proyecto (jobs, pipelines...) desplegable como unidad
- C) Una librería de Python
- D) Un tipo de cluster

**P2.** El ciclo típico de un bundle es:
- A) init → run → delete
- B) validate → deploy → run (y destroy para limpiar)
- C) commit → push → merge
- D) create → mount → share

**P3.** `mode: development` en un target:
- A) Despliega a producción
- B) Añade prefijos `[dev usuario]` a los recursos y pausa schedules para no interferir con prod
- C) Desactiva Unity Catalog
- D) Borra los datos

**P4.** En CI/CD (GitHub Actions) contra Databricks, la autenticación recomendada es:
- A) Tu token personal en las variables de CI
- B) Un service principal (client_id/secret) en secretos protegidos de CI
- C) Contraseña de usuario
- D) Sin autenticación

**P5.** Las variables del bundle (p. ej. catálogo destino) se parametrizan por:
- A) target (`dev`/`prod`) con overrides
- B) No se pueden parametrizar
- C) Editando el código a mano en cada entorno
- D) Widgets

**P6.** ¿Qué NO debería commitearse en el repo de un bundle?
- A) `databricks.yml`
- B) Notebooks de `src/`
- C) `.databricks/`, `.venv/`, tokens
- D) `resources/*.yml`

---

## Soluciones
1. **B** — DABs = IaC oficial de Databricks (en la guía nueva: Declarative Automation Bundles).
2. **B** — validate (sintaxis) → deploy (crea/actualiza) → run (ejecuta).
3. **B** — Aísla el trabajo de desarrollo sin tocar producción.
4. **B** — Service principal = identidad de máquina; tokens personales en CI son mala práctica.
5. **A** — Variables + overrides por target: mismo código, entornos distintos.
6. **C** — Estado local, entornos virtuales y secretos van al `.gitignore`.
