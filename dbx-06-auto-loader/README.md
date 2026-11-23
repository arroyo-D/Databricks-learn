# Ejercicio 6 — Auto Loader

## Objetivo
Ingestar archivos incrementalmente con `cloudFiles` (Auto Loader) y gestionar la evolución del schema.

## Conceptos
- Auto Loader = `spark.readStream.format("cloudFiles")`: detecta archivos nuevos sin releer todo
- Guarda el progreso en el **schema location / checkpoint**
- **Schema evolution**: columnas nuevas, datos rescatados en `_rescued_data`

## Tareas

### 1. Auto Loader básico (`locations_autoloader`)
- [ ] Configurar `cloudFiles.format` = json, `cloudFiles.schemaLocation`
- [ ] `cloudFiles.inferColumnTypes = true`
- [ ] Escribir en `zenviro.bronze.locations_autoloader` con `availableNow`
- [ ] Subir archivos nuevos y relanzar: solo debe procesar los nuevos

### 2. Schema evolution
- [ ] Sube un JSON con una columna NUEVA (p. ej. `postal_code`)
- [ ] Observa qué pasa por defecto y con `cloudFiles.schemaEvolutionMode`:
  - `addNewColumns` (por defecto con inferencia)
  - `rescue`
  - `failOnNewColumns`
  - `none`
- [ ] Consulta la columna `_rescued_data`

### 3. Datos mal formados
- [ ] Sube un archivo con un tipo incorrecto (texto donde va número)
- [ ] Verifica que acaba en `_rescued_data` sin romper el pipeline

### 4. Notificaciones vs listing (teoría)
- [ ] Explica en `docs/autoloader_modos.md` la diferencia entre directory listing y file notification mode

## Entregable
Notebook `notebooks/06_auto_loader.py` + nota teórica.
