# Preguntas de práctica — dbx-05 / Dominio: Ingestion (streaming) + Troubleshooting

**P1.** Un stream en Structured Streaming se conceptualiza como:
- A) Una tabla temporal
- B) Una tabla sin límites donde cada trigger añade filas nuevas
- C) Un DataFrame cacheado
- D) Una vista materializada

**P2.** El checkpointLocation garantiza:
- A) Compresión de datos
- B) Recuperación con garantía exactly-once tras fallo
- C) Cifrado del stream
- D) Menor latencia

**P3.** `trigger(availableNow=True)`:
- A) Procesa continuamente sin parar
- B) Procesa todos los datos pendientes en micro-batches y se detiene
- C) Ejecuta una vez al día
- D) Es lo mismo que never

**P4.** Output mode `complete` requiere:
- A) Una agregación; reescribe todo el resultado en cada trigger
- B) Un sink Delta obligatoriamente
- C) Un watermark
- D) Auto Loader

**P5.** Si borras el checkpoint y relanzas el stream:
- A) Continúa donde estaba
- B) Relee TODOS los datos desde el origen
- C) Falla permanentemente
- D) Solo procesa archivos de hoy

**P6.** Cambiar la lógica de una query de streaming reutilizando su checkpoint:
- A) Siempre funciona
- B) Suele fallar: solo se permiten cambios compatibles; lo correcto es un checkpoint nuevo
- C) Funciona si usas Delta
- D) Solo falla con JSON

**P7.** `maxFilesPerTrigger` sirve para:
- A) Limitar memoria del driver
- B) Controlar cuántos archivos procesa cada micro-batch (útil en pruebas)
- C) Limitar el tamaño del checkpoint
- D) Evitar archivos corruptos

---

## Soluciones
1. **B** — Unbounded table: el modelo mental del examen.
2. **B** — Checkpoint (offsets + estado) + sink idempotente = exactly-once.
3. **B** — availableNow = batch incremental bajo demanda (sustituye a trigger once).
4. **A** — complete reescribe el resultado completo: solo tiene sentido con agregaciones.
5. **B** — Sin checkpoint no hay memoria de progreso → reprocesa todo (cuidado con duplicados).
6. **B** — El checkpoint guarda la definición de la query; cambios incompatibles = error.
7. **B** — Rate limiting por trigger en fuentes de archivos.
