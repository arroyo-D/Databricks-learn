# Ejercicio 10 — Databricks SQL

## Objetivo
Usar la cara analítica de Databricks: SQL Warehouse, editor SQL, queries, dashboards, Genie y alerts sobre los datos Gold de Zenviro.


## Por qué importa

Databricks SQL es la cara analítica de la plataforma: SQL warehouses serverless, queries, dashboards y Genie. Pertenece al dominio de plataforma y es donde tus datos Gold se convierten en decisiones. Saber elegir el tamaño del warehouse, cachear resultados y montar un dashboard AI/BI es lo que separa "ingeniero que carga datos" de "ingeniero que entrega valor".

## Tareas

### 1. SQL Warehouse
- [ ] Crear un SQL Warehouse (tamaño 2X-Small, serverless si está disponible)
- [ ] Diferenciar: SQL Warehouse vs cluster all-purpose

### 2. SQL Editor y Queries
- [ ] Ejecutar queries sobre `zenviro.gold` en el SQL Editor
- [ ] Guardar queries con nombre y descripción
- [ ] Revisar el **Query History**: duración, usuario, warehouse
- [ ] Programar refresco de una query

### 3. Dashboard
- [ ] Crear un dashboard AI/BI con 3 visualizaciones:
  - Gasto salarial por ubicación (barras)
  - Empleados por departamento (tabla)
  - Beneficios por empleado (de tu tarea del ejercicio 3)
- [ ] Añadir un filtro (parámetro) por país
- [ ] Publicar y programar refresco del dashboard

### 4. Genie
- [ ] Crear un espacio Genie sobre las tablas gold
- [ ] Hacer 3 preguntas en lenguaje natural y revisar el SQL generado

### 5. Alerts
- [ ] Crear una alerta: avisar si el gasto salarial total de alguna ubicación supera un umbral
- [ ] Configurar destino de notificación (email)

## Criterios de éxito

- [ ] SQL warehouse serverless creado con auto-stop configurado.
- [ ] Dashboard AI/BI con 3+ visualizaciones sobre tu Gold.
- [ ] Pregunta en lenguaje natural resuelta con Genie (y entendido por qué funciona).

## Recursos

- Documentación oficial: https://docs.databricks.com/ (*Data warehousing*, *AI/BI*)

## Entregable
Exportar definiciones del dashboard si es posible + capturas en `docs/`.