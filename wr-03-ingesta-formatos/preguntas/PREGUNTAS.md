# Preguntas de práctica — wr-03 / Ingesta de formatos

**P1.** El problema principal de ingerir CSV a gran escala:
- A) Es binario
- B) No tiene esquema ni tipos: todo es texto hasta que lo parseas (y hay inferencias erróneas)
- C) No se puede comprimir
- D) No lo lee Spark

**P2.** Ventaja clave de Parquet sobre CSV en el lakehouse:
- A) Es legible por humanos
- B) Columnar, comprimido, con esquema y estadísticas (data skipping)
- C) Ocupa más pero es más rápido
- D) Solo funciona con pandas

**P3.** JSON anidado en Spark se maneja con:
- A) `explode()` para arrays y notación de punto/`from_json` con esquema para structs
- B) Expresiones regulares
- C) Imposible: hay que aplanarlo fuera
- D) `COPY INTO` directo a columnas

**P4.** Al leer un CSV con `inferSchema=True`:
- A) Siempre acierta los tipos
- B) Hace una pasada extra sobre los datos y puede inferir mal (mejor: esquema explícito)
- C) Solo funciona en clusters grandes
- D) Es obligatorio en producción

**P5.** XML/HTML mal formado en scraping se parsea con:
- A) `xml.etree` estricto
- B) `BeautifulSoup` (tolerante con HTML roto del mundo real)
- C) `json.loads`
- D) `open().read()` y split

**P6.** ¿Por qué guardar la respuesta cruda en Bronze antes de transformar?
- A) Ocupa más y no aporta
- B) Reprocesabilidad: si tu parseo tiene un bug, la fuente original sigue intacta
- C) Lo exige GDPR
- D) Para backups de Spark

**P7.** Un Excel con varias hojas y cabeceras desplazadas:
- A) No se puede ingerir
- B) `pd.read_excel(..., sheet_name=, header=)` y limpieza en Silver; nunca editar el Excel a mano
- C) Convertir a CSV con Excel primero siempre
- D) Solo Auto Loader

**P8.** El delimitador, encoding y comillas de un CSV problemático se controlan con:
- A) Suerte
- B) Opciones `sep`, `encoding`, `quote`/`escape` (o `csv.Sniffer` como ayuda, nunca a ciegas)
- C) Renombrando el archivo
- D) pandas no lo permite

---

## Soluciones
1. **B** — CSV = texto sin tipos: la trampa nº 1 de la ingesta.
2. **B** — Columnar + esquema + stats: lecturas más rápidas y selectivas.
3. **A** — Structs con esquema, arrays con explode.
4. **B** — Inferir cuesta y falla: esquema explícito en pipelines serios.
5. **B** — BeautifulSoup está hecho para HTML real roto.
6. **B** — Bronze inmutable = capacidad de rehacer Silver/Gold.
7. **B** — Parametrizar la lectura y limpiar después; jamás edición manual de la fuente.
8. **B** — Opciones explícitas de parseo, documentadas.
