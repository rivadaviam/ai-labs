# Lab 10: DocSense — Extractor Inteligente de Documentos Técnicos

**Duración del equipo:** 2–3 personas
**Nivel:** Intermedio

---

## 1. Nombre del lab

**DocSense** — Pipeline de extracción estructurada de información desde documentos no estructurados con schema configurable

---

## 2. Skill principal

**Structured Outputs + Document Understanding** — Uso de LLMs como parsers inteligentes de documentos complejos con schemas de salida forzada (JSON Schema / Pydantic) y scoring de confianza por campo.

---

## 3. Problema a resolver

El equipo de DevOps o architecture recibe entre 20 y 80 páginas de especificaciones técnicas (RFCs, architecture decision records, vendor contracts, runbooks, change requests) en PDF. Extraer manualmente los datos críticos — versiones de dependencias, SLAs, endpoints, configuraciones obligatorias, fechas de deprecación, contactos de escalación — toma 2-3 horas por documento. Con volumen alto se pierden datos importantes. Peor: distintas personas extraen los mismos campos de forma inconsistente.

---

## 4. Producto objetivo

**Una CLI + API REST** que recibe un PDF o un directorio de PDFs, extrae entidades estructuradas según un schema configurable, y devuelve JSON limpio con un campo de confianza por cada valor extraído.

**Demo en 15 minutos:** Ejecutar `docsense extract --schema infra_spec.yaml documento_vendor.pdf`. El output es un JSON estructurado con todos los campos del schema (versión de API, SLA en horas, región de deploy, contacto de escalación, fecha de EOL), cada uno con su `value` y `confidence` score. Luego ejecutar `docsense report resultado.json` que genera un reporte HTML donde los campos con confianza < 0.70 aparecen resaltados en amarillo como "verificar manualmente." El demo muestra también cómo cambiar el schema YAML para extraer un tipo diferente de documento sin tocar el código.

---

## 5. Alcance del MVP

- CLI con comando `extract` que acepta PDF o directorio + archivo de schema
- Schema configurable en YAML: el usuario define nombre del campo, tipo (string, date, number, list), descripción en lenguaje natural, y si es obligatorio
- Procesamiento de PDF: extracción de texto con PyMuPDF, chunking por secciones preservando títulos como contexto
- Llamada al LLM con output forzado a JSON Schema (usando `response_format` en OpenAI, `tool_use` en Anthropic, o grammar sampling en Ollama)
- Confidence score por campo: el LLM genera junto con el valor una justificación y un score 0.0–1.0
- Output: JSON estructurado + reporte HTML con campos resaltados según nivel de confianza
- API REST con FastAPI: endpoint `POST /extract` que acepta PDF como multipart/form-data y devuelve el JSON

---

## 6. Extensión

- Modo batch: procesar carpeta de 50+ documentos, output consolidado en CSV/parquet para análisis
- Detección automática de tipo de documento (RFC, contrato, runbook) y selección automática de schema pre-definido
- Sistema de validación y ground truth: cargar annotations manuales y medir field-level accuracy del extractor
- Interface web (Streamlit o Gradio) para subir documentos, ver la extracción en tiempo real con highlighting del texto fuente
- Integración con Notion o Confluence API para publicar extracciones directamente en páginas de documentación

---

## 7. Stack sugerido

**Gratis / open-source:**
- Ollama + Mistral 7B o Llama3 con grammar sampling para JSON forzado
- PyMuPDF (fitz) para extracción de texto de PDFs
- Pydantic v2 para schema validation y generación de JSON Schema
- Typer para CLI
- FastAPI para la API REST
- Jinja2 para reporte HTML

**Alternativas cloud:**
- OpenAI GPT-4o con `response_format: { type: "json_schema" }` (muy confiable para extracción)
- Anthropic Claude con pattern `tool_use` para structured extraction
- Google Gemini 1.5 Flash (context window de 1M tokens, ideal para documentos largos, económico)

---

## 8. Terreno a explorar

- ¿Por qué la extracción de texto de PDFs con tablas multi-columna es difícil? ¿Qué información se pierde y cómo afecta al LLM?
- ¿Cómo se calibra un confidence score en extracción de documentos? ¿Qué significa que el modelo reporte 0.90 para todo?
- ¿Qué pasa cuando un campo aparece múltiples veces con valores distintos en el mismo documento? ¿Cuáles son las estrategias de resolución y sus trade-offs?
- ¿Cuándo conviene forzar output estructurado con grammar sampling vs. confiar en el prompt y few-shot examples?
- ¿Cómo se diseña un schema de extracción que sea portable entre tipos de documentos distintos sin cambiar el código?
- ¿Cuál es la relación entre el tamaño del documento, el context window del modelo, y la latencia de procesamiento?
- ¿Qué hace que un documento sea difícil de extraer incluso para un modelo grande?

---

## 9. Reflexión AI (opcional)

Template para documentar el proceso de aprendizaje. No es un entregable obligatorio — se completa si el equipo decide hacerlo o si se acuerda un write-up posterior al show & tell.

```
## Reflexión AI — DocSense — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| | | |

### Mayor impacto de IA en este lab
[ej: diseñar el schema YAML de extracción, escribir los few-shot examples del prompt, generar el reporte HTML]

### Momento donde la IA no ayudó o introdujo problemas
[ej: el LLM ignoró el schema en documentos con formato complejo, o los confidence scores eran siempre 0.9]

### ¿La IA cambió cómo trabajó el equipo (no solo lo aceleró)?
[ ] Sí — describir: ___
[ ] No, solo aceleró tareas existentes

### Recomendación para el próximo equipo
[ej: "Invertí tiempo en few-shot examples reales antes de probar en documentos difíciles — marca la diferencia"]
```
