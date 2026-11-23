# Solución — Ejercicio 10

## 1. SQL Warehouse
SQL → Warehouses → Create: 2X-Small, serverless, auto-stop 10 min.
- SQL Warehouse: SQL analítico, multiusuario, arranque rápido; ideal para BI.
- All-purpose: desarrollo interactivo (notebooks), data engineering.

## 2. Queries
```sql
SELECT city, country, num_empleados, gasto_salarial
FROM zenviro.gold.gasto_por_ubicacion
ORDER BY gasto_salarial DESC;
```
Guardar con nombre, añadir descripción y programar refresh (Scheduled query).
Query History: SQL → Query History (filtro por warehouse/usuario, duración, bytes leídos).

## 3. Dashboard AI/BI
1. Dashboards → Create → añadir datasets (queries sobre gold).
2. Canvas: gráfico de barras (city × gasto_salarial), tabla departamentos, tabla beneficios.
3. Filtro: campo `country` como parámetro/filtro global del dashboard.
4. Publish → elegir credenciales (las tuyas o las del viewer) y schedule de refresco.

## 4. Genie
Genie → Create space → añadir tablas `zenviro.gold.*`, instrucciones en texto (sinónimos de negocio).
Ejemplos de preguntas: "¿qué ubicación gasta más en salarios?", "empleados por departamento", "top 5 beneficios más usados".
Revisar siempre el SQL generado antes de fiarte del número.

## 5. Alerts
Alerts → Create alert:
```sql
SELECT city, gasto_salarial FROM zenviro.gold.gasto_por_ubicacion
```
- Condición: `gasto_salarial > 500000` → notify email.
- Frecuencia: cada hora / al refrescar la query.

## Puntos de examen
- Serverless warehouse arranca en segundos y factura por uso.
- Alert = query + condición + frecuencia + destino.
- Dashboards AI/BI sustituyen a los legacy dashboards.
- Genie necesita buenas instrucciones/descripciones para generar SQL correcto.
