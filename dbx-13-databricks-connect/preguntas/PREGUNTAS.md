# Preguntas de práctica — dbx-13 / Dominio: CI/CD (Databricks Connect)

**P1.** Databricks Connect permite:
- A) Ejecutar Spark en local sin cluster
- B) Ejecutar código desde tu IDE local usando el cómputo del cluster remoto
- C) Crear clusters
- D) Sustituir a Unity Catalog

**P2.** La versión del paquete `databricks-connect` debe:
- A) Ser siempre la última
- B) Coincidir con la versión mayor.menor del Databricks Runtime del cluster
- C) Ser menor que el runtime
- D) Da igual

**P3.** Las credenciales para Connect se configuran en:
- A) El código fuente, commiteado a Git
- B) `~/.databrickscfg` o variables de entorno — nunca en el código
- C) Un archivo `secrets.txt` en el repo
- D) El notebook remoto

**P4.** Con Connect, ¿qué NO funciona igual que en un notebook?
- A) `spark.table()`
- B) `display()` de notebook y algunos `dbutils` (p. ej. widgets)
- C) `df.filter()`
- D) `spark.sql()`

**P5.** Databricks Connect es especialmente útil para:
- A) Producción masiva
- B) Desarrollo local con IDE, debugging y tests unitarios (pytest)
- C) Dashboards
- D) Streaming continuo

---

## Soluciones
1. **B** — Local escribe, remoto ejecuta (gRPC).
2. **B** — Mismatch de versión = errores de compatibilidad.
3. **B** — PAT en fichero de config local o env vars; jamás en Git.
4. **B** — `display()` es del entorno notebook; en local usa `.show()`/`.collect()`.
5. **B** — Es una herramienta de desarrollo, no de orquestación productiva.
