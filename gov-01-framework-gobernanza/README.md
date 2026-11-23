# GOV-01 — Encuentra tu Framework de Gobernanza

> Capítulo 1 de *Designing Data Governance from the Ground Up* (Maffeo), trasladado a Databricks + Unity Catalog.

## Objetivo
Definir qué es la gobernanza para TU organización y elegir un framework, mapeándolo a las capacidades de Unity Catalog.

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

## Entregable
`docs/` con: definicion.md, framework.md, mapeo_uc.md, inventario.md + notebook `notebooks/gov01_inventario.py`.
