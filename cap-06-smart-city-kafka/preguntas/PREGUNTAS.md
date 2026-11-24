# Preguntas de práctica — cap-06 / Smart City con Kafka

**P1.** Kafka frente a ficheros para telemetría IoT aporta:
- A) Más formato
- B) Un log distribuido de eventos con retención y replay: múltiples consumidores a su ritmo sin perder eventos
- C) Menos red
- D) SQL

**P2.** Los 5 topics separados (vehicles, gps, traffic, weather, emergencies) existen porque:
- A) Kafka lo exige
- B) Cada fuente tiene ritmo, esquema y consumidores distintos: separación por dominio de evento
- C) Es estético
- D) Los topics son gratis

**P3.** El watermark en la ventana de tráfico de 5 minutos define:
- A) El tamaño del cluster
- B) Cuánto esperas a eventos tardíos antes de cerrar el cálculo de la ventana: trade-off latencia vs completitud
- C) El nº de particiones
- D) El formato de fecha

**P4.** Las emergencias (eventos raros) en un sistema de ventanas son el caso que prueba:
- A) El color del dashboard
- B) Que el sistema no solo funciona "en media": un evento raro pero crítico debe procesarse y alertar en segundos
- C) El coste
- D) La compresión

**P5.** En Free Edition, Kafka en tu servidor se conecta a Databricks vía:
- A) `readStream.format("kafka")` directo
- B) File-drop (consumidor → Volume → Auto Loader) o pipeline 100% local con PySpark: la salida restringida de serverless no alcanza tu IP
- C) VPN
- D) No se puede hacer el proyecto

**P6.** Las alertas SQL de emergencias se complementan con:
- A) Un email semanal
- B) Un dashboard Gold actualizado + regla de alerta que dispara acción (webhook) en segundos, no un informe al día siguiente
- C) Un PDF
- D) Nada

---

## Soluciones
1. **B** — Log con replay y consumidores independientes.
2. **B** — Topics por dominio: ritmo y esquema por fuente.
3. **B** — Watermark = latencia vs completitud declarada.
4. **B** — Lo raro pero crítico: el test real del sistema.
5. **B** — File-drop o local: la restricción de salida manda.
6. **B** — Alerta accionable en segundos: las emergencias no esperan al informe.
