# Preguntas de práctica — dbx-10 / Dominio: Platform + Production (Databricks SQL)

**P1.** Un SQL Warehouse frente a un all-purpose cluster:
- A) Es lo mismo
- B) Optimizado para SQL/BI multiusuario, arranque rápido y auto-stop
- C) No ejecuta queries concurrentes
- D) Solo sirve para streaming

**P2.** Un warehouse serverless:
- A) Requiere gestionar VMs
- B) Arranca en segundos y factura por uso
- C) No soporta Unity Catalog
- D) Es gratuito

**P3.** Una alerta en Databricks SQL se compone de:
- A) Solo un email
- B) Query + condición sobre el resultado + frecuencia de evaluación + destino
- C) Un notebook programado
- D) Un job de Lakeflow obligatoriamente

**P4.** Query History permite:
- A) Editar queries antiguas automáticamente
- B) Auditar queries: usuario, duración, warehouse, bytes leídos
- C) Recuperar tablas borradas
- D) Versionar dashboards

**P5.** Genie es:
- A) Un motor de ETL
- B) Una interfaz de lenguaje natural que genera SQL sobre tus tablas
- C) Un sistema de permisos
- D) Un formato de archivo

**P6.** Al publicar un dashboard AI/BI puedes:
- A) Solo verlo tú
- B) Compartirlo y elegir si los viewers usan tus credenciales o las suyas
- C) Publicarlo sin gobierno de acceso
- D) Exportarlo solo a PDF

---

## Soluciones
1. **B** — Warehouse = compute SQL optimizado para BI; all-purpose = desarrollo/ingeniería.
2. **B** — Serverless = gestión cero, arranque rápido, pago por uso.
3. **B** — Query + condition + schedule + destination: la definición exacta que pide el examen.
4. **B** — Herramienta clave de troubleshooting y auditoría.
5. **B** — Genie = NL→SQL; siempre revisar el SQL generado.
6. **B** — El modelo de credenciales importa: define qué datos ve cada viewer.
