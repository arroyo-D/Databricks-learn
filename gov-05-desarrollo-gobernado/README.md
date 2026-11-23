# GOV-05 — Desarrollo Guiado por la Gobernanza

> Capítulo 5 de Maffeo: construir CON gobernanza desde el diseño, no parchear después.

## Objetivo
Integrar la gobernanza en el flujo de desarrollo: naming, convenciones, entornos y revisión por pares.


## Por qué importa

Gobernar el desarrollo significa que cada pipeline nuevo nace ya gobernado: catálogo correcto, convenciones de nombre, propietario asignado, calidad declarada y revisión por pares. Este módulo convierte los módulos dbx en práctica gobernada: las convenciones que escribas aquí son las que luego aplicarás en los capstones sin pensar.

## Tareas

### 1. Convenciones
- [ ] `docs/convenciones.md`: naming de tablas (`dominio_entidad`, snake_case), columnas, schemas; tipos permitidos por capa; quién aprueba qué
- [ ] Valida tus tablas existentes contra la convención con una query (nombres que no cumplan snake_case, sin prefijo de capa...)

### 2. Entornos
- [ ] Separa dev/prod: catálogo `zenviro_dev` para desarrollo, `zenviro` para prod
- [ ] Regla: nada entra en prod sin pasar por dev

### 3. Infraestructura mínima viable
- [ ] Checklist del libro: ¿tienes lo necesario? (compute con UC, storage gobernado, secrets, CI)
- [ ] Documenta gaps en `docs/infra_gaps.md`

### 4. Governance-driven development (práctica)
- [ ] Desarrolla UNA tabla nueva de principio a fin siguiendo el flujo gobernado:
  1. Definición en el diccionario ANTES de crear la tabla
  2. Creación en dev con comentarios y tags
  3. Expectations de calidad
  4. Revisión (PR en GitHub con el DDL)
  5. Deploy a prod
- [ ] Documenta cada paso con capturas

### 5. Room to fail
- [ ] Crea un schema `sandbox` con permisos amplios para experimentación y cuarentena de datos dudosos

## Criterios de éxito

- [ ] Convenciones de nombrado y estructura de catálogos escritas y aplicadas.
- [ ] Checklist de "pipeline gobernado" usado en un ejercicio del track dbx.
- [ ] Pull request revisado con la checklist (rol de revisor incluido).

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Data governance*, *Develop on Databricks*)

## Entregable
`docs/convenciones.md` + `notebooks/gov05_nueva_tabla.sql` documentando el flujo completo + PR de ejemplo.