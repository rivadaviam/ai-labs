# Lab 10: DocSense — Extractor Inteligente de Documentos Técnicos

**Equipo:** 2–3 personas
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

**Validaciones sugeridas:**

- Ejecutar la extracción sobre un documento de prueba y verificar que el JSON de output respeta el schema definido
- Verificar que los confidence scores reflejan la dificultad real de extracción (campos ambiguos deberían tener score bajo)
- Cambiar el schema YAML para otro tipo de documento y verificar que funciona sin cambiar código
- Generar el reporte HTML y verificar que los campos de baja confianza se destacan visualmente
- Probar con al menos 2 documentos de complejidad distinta para evaluar robustez

*En el show & tell, el equipo muestra las validaciones que corrió y centra la conversación en decisiones tomadas, problemas encontrados, y lecciones aprendidas.*

---

## 5. Alcance del MVP

**Primera fase — Extracción básica con schema configurable:**
- CLI con comando `extract` que acepta PDF o directorio + archivo de schema
- Schema configurable en YAML: el usuario define nombre del campo, tipo (string, date, number, list), descripción en lenguaje natural, y si es obligatorio
- Procesamiento de PDF: extracción de texto con PyMuPDF, chunking por secciones preservando títulos como contexto
- Llamada al LLM con output forzado a JSON Schema

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas.

**Segunda fase — Confidence scores, reporte HTML y API:**
- Confidence score por campo: el LLM genera junto con el valor una justificación y un score 0.0–1.0
- Output: JSON estructurado + reporte HTML con campos resaltados según nivel de confianza
- API REST con FastAPI: endpoint `POST /extract` que acepta PDF como multipart/form-data y devuelve el JSON

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo.

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
- ¿La IA ayudó a diseñar el schema YAML y los few-shot examples, o el equipo tuvo que iterar manualmente hasta que la extracción fuera confiable?
- ¿En qué parte del lab la IA ahorró más tiempo: parsing de PDFs, diseño del prompt de extracción, o construcción de la API REST?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — DocSense — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Codex / Copilot | Implementar el parsing de PDFs y la API REST | |
| Claude / ChatGPT | Diseñar el schema YAML y los few-shot examples del prompt de extracción | |
| Windsurf / Cursor | Construir la CLI y el generador de reporte HTML | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en diseñar el schema YAML de extracción, en escribir los few-shot
examples del prompt, en implementar el parsing de PDFs, o en generar el reporte HTML?]

### Momento donde la IA no ayudó o introdujo problemas
[¿El LLM ignoró el schema en documentos con formato complejo? ¿Los confidence scores
eran siempre 0.9 independientemente de la dificultad? ¿El parsing de PDFs con tablas
perdía información crítica?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Invertí tiempo en few-shot examples reales antes de probar en documentos difíciles
— marca la diferencia. El parsing de PDFs con tablas es el mayor obstáculo técnico:
probá con PyMuPDF y pdfplumber y elegí el que mejor preserve la estructura."]
```
