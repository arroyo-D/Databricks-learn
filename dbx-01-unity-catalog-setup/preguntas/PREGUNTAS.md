# Preguntas de práctica — dbx-01 / Dominio: Governance & Security (15%)

**P1.** La jerarquía de objetos de Unity Catalog es:
- A) Catalog → Metastore → Schema → Table
- B) Metastore → Catalog → Schema → Table/View/Volume/Function
- C) Workspace → Catalog → Database → Table
- D) Metastore → Schema → Catalog → Table

**P2.** ¿Cuántos metastores de Unity Catalog puede haber por región en una cuenta?
- A) Ilimitados
- B) Uno por workspace
- C) Uno por región
- D) Uno por catálogo

**P3.** Un Storage Credential en UC define:
- A) Qué usuarios pueden leer una tabla
- B) Cómo se autentica Databricks ante el cloud storage (identidad/rol)
- C) La ruta del contenedor externo
- D) El cifrado de las tablas

**P4.** Para crear una External Location necesitas:
- A) Solo la URL del storage
- B) Una URL de storage + un Storage Credential
- C) Un metastore dedicado
- D) Un cluster serverless

**P5.** Privilegios MÍNIMOS para que un usuario consulte una tabla en UC:
- A) SELECT sobre la tabla
- B) USE CATALOG + USE SCHEMA + SELECT
- C) ALL PRIVILEGES
- D) MODIFY + SELECT

**P6.** Un cluster con modo de acceso "Shared" y UC permite:
- A) Solo un usuario a la vez
- B) Múltiples usuarios con aislamiento y gobierno UC
- C) Acceso sin autenticación
- D) Solo SQL, nada de Python

---

## Soluciones
1. **B** — Metastore (región) → Catalog → Schema → objetos.
2. **C** — Un metastore por región; puede compartirse entre workspaces de esa región.
3. **B** — Credential = identidad (managed identity/SP); Location = URL + credential.
4. **B** — External Location siempre referencia un Storage Credential.
5. **B** — Clásica pregunta de examen: hacen falta los tres privilegios.
6. **B** — Shared = multiusuario con aislamiento; Single user = un solo usuario asignado.
