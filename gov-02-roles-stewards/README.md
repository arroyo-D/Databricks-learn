# GOV-02 — Data Stewards y Roles

> Capítulo 2 de Maffeo: qué son los data stewards y cómo implementarlos en Unity Catalog con grupos y ownership.

## Objetivo
Pasar del concepto "data steward" a una implementación técnica real: grupos, owners y responsabilidades por dominio en UC.

## Tareas

### 1. Define los roles
- [ ] Matriz RACI para Zenviro en `docs/raci.md`: para cada dominio (hr, finance, ops) — owner, steward, consumidores
- [ ] ¿Qué hace un steward y qué NO hace? (no es soporte técnico ni helpdesk)

### 2. Grupos en la cuenta
- [ ] Crea los grupos en Account Console: `hr_stewards`, `finance_stewards`, `data_engineers`, `data_analysts`
- [ ] Añade tu usuario a `data_engineers`

### 3. Ownership técnico
- [ ] Asigna owners: `ALTER SCHEMA ... OWNER TO` por dominio
- [ ] Verifica con `DESCRIBE SCHEMA EXTENDED`

### 4. Permisos por rol
- [ ] Stewards: `USE` + `SELECT` + `MODIFY` en su dominio (pueden documentar y corregir)
- [ ] Analysts: solo `SELECT` en gold
- [ ] Engineers: `ALL PRIVILEGES` o el mínimo necesario en todo el catálogo
- [ ] Verifica la matriz resultante con `SHOW GRANTS`

### 5. Responsabilidades medibles
- [ ] Define 3 KPIs de stewardship (p. ej. % tablas con descripción, % con tags de dominio, % columnas documentadas)
- [ ] Escribe la query que mide cada KPI sobre `system.information_schema`

## Entregable
`docs/raci.md` + notebook `notebooks/gov02_roles.sql` con grants + `notebooks/gov02_kpis.sql`.
