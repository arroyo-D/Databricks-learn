# GOV-01 — Encuentra tu Framework de Gobernanza

> Capítulo 1 de *Designing Data Governance from the Ground Up* (Maffeo), trasladado a Databricks + Unity Catalog.

## Objetivo
Definir qué es la gobernanza para TU organización y elegir un framework, mapeándolo a las capacidades de Unity Catalog.


## Por qué importa

La gobernanza no es burocracia: es lo que permite que los datos (y la IA) lleguen a producción sin sustos legales ni de calidad. El libro de Maffeo propone empezar pequeño con un framework propio; en Databricks ese framework se apoya en Unity Catalog. Las empresas que gobiernan activamente llevan 12x más proyectos de IA a producción (*State of AI Agents*). Este módulo diseña tu framework mínimo viable.

## Contexto
Usa la empresa ficticia **Zenviro** (RRHH) de los ejercicios dbx-XX como tu organización.

## Tareas

### 1. Define gobernanza
- [ ] Escribe en `docs/definicion.md` tu propia definición de gobernanza de datos (máx. 3 frases), alineada con la misión de Zenviro

### 2. Elige framework
- [ ] Compara 3 frameworks (DAMA-DMBOK, DCAM, y el modelo por dominios/data mesh) en una tabla: para quién funciona, fortalezas, debilidades
- [ ] Elige uno para Zenviro y justifícalo

### 3. Mapeo a Unity Catalog
- [ ] Tabla de correspondencia: concepto del framework → capacidad de UC (catálogos por dominio, owners, tags, linaje, audit, permisos)

### 4. Primer inventario
- [ ] Lista con SQL los objetos existentes en tu metastore (catálogos, schemas, tablas) y clasifícalos: ¿gobernados o huérfanos?
- [ ] Propón una estructura de catálogos por dominio para Zenviro (hr, finance, operations...)

### 5. Tu siguiente paso
- [ ] Define 3 acciones concretas para la semana siguiente (siguiendo el "Your Next Step" del libro)

## Criterios de éxito

- [ ] Framework escrito: dominios, principios y políticas iniciales (1-2 páginas).
- [ ] Cada política mapeada a un mecanismo concreto de Databricks/UC.
- [ ] Un riesgo de datos de tu organización (real o ficticia) documentado con su mitigación.

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Data governance with Unity Catalog*)

## Entregable
`docs/` con: definicion.md, framework.md, mapeo_uc.md, inventario.md + notebook `notebooks/gov01_inventario.py`.