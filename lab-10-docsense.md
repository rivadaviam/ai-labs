# Lab 10: DocSense — Extractor Inteligente de Documentos Técnicos

**Duración del equipo:** 2–3 personas
**MVP:** 1 semana | **Extensión:** 1 mes
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

## 5. Alcance del MVP (1 semana)

- CLI con comando `extract` que acepta PDF o directorio + archivo de schema
- Schema configurable en YAML: el usuario define nombre del campo, tipo (string, date, number, list), descripción en lenguaje natural, y si es obligatorio
- Procesamiento de PDF: extracción de texto con PyMuPDF, chunking por secciones preservando títulos como contexto
- Llamada al LLM con output forzado a JSON Schema (usando `response_format` en OpenAI, `tool_use` en Anthropic, o grammar sampling en Ollama)
- Confidence score por campo: el LLM genera junto con el valor una justificación y un score 0.0–1.0
- Output: JSON estructurado + reporte HTML con campos resaltados según nivel de confianza
- API REST con FastAPI: endpoint `POST /extract` que acepta PDF como multipart/form-data y devuelve el JSON

---

## 6. Extensión (1 mes)

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

## 8. Criterios de evaluación

| Criterio | Métrica mínima de éxito |
|---|---|
| Extracción básica | 8/10 campos correctos en el documento de prueba estándar del lab |
| Confianza calibrada | Campos con confianza < 0.60 son incorrectos o ambiguos en ≥ 70% de los casos |
| Portabilidad de schema | 3 schemas distintos (infra spec, vendor contract, runbook) funcionan sin modificar el código |
| Latencia | Documento de 20 páginas procesado en < 45 segundos end-to-end |
| CLI usabilidad | Un usuario nuevo puede usar la CLI para extraer un documento sin leer documentación en < 5 minutos |

---

## 9. Riesgos y desafíos

- **PDFs con tablas multi-columna** — La extracción de texto plano de tablas suele fallar o producir texto mezclado. Solución: usar pdfplumber para detectar tablas y procesarlas como markdown separado antes de incluirlas en el contexto del LLM.
- **El LLM ignora el schema y responde en texto libre** — En modelos pequeños esto es frecuente. Solución: usar grammar sampling en Ollama para forzar JSON válido, o agregar few-shot examples en el prompt que demuestren el formato exacto de salida.
- **Campos que aparecen múltiples veces** — El LLM toma el primero o el último arbitrariamente (ej. versiones en distintas secciones del doc). Hay que decidir una estrategia explícita: todos los valores como lista, el valor más reciente, el del sumario ejecutivo.
- **Confidence score sin calibrar** — El LLM tiende a reportar 0.90 para todo si no se lo guía. El prompt debe incluir ejemplos de cuándo la confianza debería ser baja (información ambigua, campo ausente, valor inferido).

---

## 10. Reflexión AI (completar al terminar el lab)

Cada equipo completa este template y lo comparte con el programa. Alimenta el loop de mejora entre ciclos.

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
