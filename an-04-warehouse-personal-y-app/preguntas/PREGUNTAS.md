# Preguntas de práctica — an-04 / Warehouse en estrella + app

**P1.** En un esquema en estrella, la tabla de hechos contiene:
- A) Descripciones de las entidades
- B) Mediciones/eventos cuantitativos con claves foráneas a las dimensiones (qué pasó, cuánto, cuándo)
- C) Metadatos
- D) Índices

**P2.** `dim_fecha` existe porque:
- A) Ocupa poco
- B) Centraliza atributos de calendario (trimestre, estación, festivo) que todas las queries reutilizan consistentemente
- C) Es obligatoria por SQL
- D) Sustituye a los timestamps

**P3.** Una carga incremental idempotente se implementa con:
- A) `append` sin comprobar
- B) `MERGE INTO` por clave natural (fecha+ciudad): reejecutar el mismo día no duplica
- C) `overwrite` diario de toda la tabla
- D) Truncar y cargar

**P4.** PK/FK declarativas y `COMMENT ON COLUMN` en Databricks mejoran a Genie porque:
- A) Aceleran las queries
- B) Le dan el modelo semántico: qué tablas se unen cómo y qué significa cada columna
- C) Son decorativos
- D) Solo sirven para el examen

**P5.** El loop de mejora de Genie es:
- A) Rezar
- B) Pregunta → fallo → añadir instrucción/sinónimo/documentación → volver a preguntar (y documentar los ciclos)
- C) Cambiar de modelo
- D) Reinstalar

**P6.** Las credenciales en una app Streamlit desplegada:
- A) Hardcodeadas en app.py
- B) Variables de entorno/secrets del sistema de despliegue, nunca en el código
- C) En el README
- D) En un CSV

**P7.** Por qué los exports personales (Spotify, banco) NO van a Free Edition:
- A) Ocupan mucho
- B) Sus términos permiten entrenar modelos con tu contenido: datos privados → solo en tu servidor local
- C) Son ilegales
- D) No los lee Spark

**P8.** Una buena app de datos se reconoce porque:
- A) Tiene muchos filtros
- B) Un tercero entiende qué ve en 10 segundos: KPI arriba, gráfico principal, narrativa clara
- C) Usa 3D
- D) Carga 50 gráficos

---

## Soluciones
1. **B** — Hechos = mediciones + FKs; dims = contexto descriptivo.
2. **B** — Calendario consistente y compartido.
3. **B** — MERGE = idempotencia incremental.
4. **B** — Semántica explícita = Genie acierta.
5. **B** — Instrucciones iterativas documentadas: así se "entrena" Genie.
6. **B** — Secrets del despliegue, jamás en código.
7. **B** — Privacidad: Free Edition puede entrenar con tu contenido.
8. **B** — El test de los 10 segundos.
