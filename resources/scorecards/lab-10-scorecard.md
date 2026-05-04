# Scorecard — Lab 10: DocSense

**Skill principal:** Structured Outputs + Document Understanding
**Patrón de IA:** LLM como parser inteligente con JSON Schema forzado y confidence scoring por campo
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | CLI + API REST demostrable en 15 minutos: `docsense extract --schema infra_spec.yaml doc.pdf` con output JSON + reporte HTML |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP acotado a CLI, extracción de texto con PyMuPDF, llamada al LLM con output forzado, confidence score, reporte HTML y un endpoint FastAPI |
| 3 | Anclado en patrón de IA real | ✅ | Structured outputs con JSON Schema / tool_use / grammar sampling — patrón vigente y ampliamente usado en producción |
| 4 | Problema realista | ✅ | Extracción manual de RFCs, ADRs, contratos vendor, runbooks; 2–3 horas por documento con inconsistencia entre personas |
| 5 | Requiere decisiones técnicas | ✅ | Diseño del schema YAML, estrategia de chunking, manejo de campos repetidos con valores distintos, calibración de confidence scores, elección de método de forcing |
| 6 | Comparable entre equipos | ✅ | Output JSON estructurado + field-level confidence permite comparar resultados entre equipos con los mismos documentos de prueba |
| 7 | Stack al servicio del concepto | ✅ | PyMuPDF, Pydantic, Typer, FastAPI son herramientas convencionales sin overhead de aprendizaje; el foco es el patrón de extracción |
| 8 | AWS preferido pero no obligatorio | ⚠️ | No menciona AWS en absoluto; stack es 100% local u OpenAI/Anthropic/Gemini — válido pero omite opciones como Textract o Bedrock |
| 9 | Costo AWS mínimo | ✅ | Stack gratuito con Ollama local; alternativas cloud son APIs por consumo sin infraestructura fija; costo estimado muy por debajo de $5–10 |

**Diseño: 8/9 ✅ · 1/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | El objetivo es construir un extractor funcional, no responder preguntas de certificación |
| 2 | No es walkthrough sin producto | ✅ | Producto concreto con CLI, API REST y reporte HTML demostrable en 15 minutos |
| 3 | No depende de datos sensibles | ✅ | El lab sugiere usar documentos técnicos propios (RFCs, ADRs) sin requerir datos de producción; los documentos de prueba pueden ser públicos |
| 4 | No requiere infra costosa | ✅ | Ollama local como opción primaria; alternativas cloud son pay-per-use sin costo fijo |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Sección Reflexión AI explícita con preguntas sobre herramientas usadas, mayor impacto y momentos donde la IA no ayudó |
| 6 | No asume nivel homogéneo | ✅ | Sección Extensión provee trabajo adicional para equipos avanzados (batch mode, ground truth, interface web, integración con Notion/Confluence) |

**Anti-criterios: 6/6 pasan**

---

## Estructura del Lab

| # | Sección | Estado |
|---|---|---|
| 1 | Nombre | ✅ |
| 2 | Skill principal | ✅ |
| 3 | Problema a resolver | ✅ |
| 4 | Producto objetivo | ✅ |
| 5 | Alcance del MVP | ✅ |
| 6 | Extensión | ✅ |
| 7 | Stack sugerido | ✅ |
| 8 | Terreno a explorar | ✅ |
| 9 | Reflexión AI | ✅ |

**Estructura: 9/9 secciones presentes**

---

## Resumen

**Score general:** 23/24 (diseño 8/9 + anti-criterios 6/6 + estructura 9/9)

### Fortalezas
- Demo de 15 minutos extraordinariamente bien definido: comando exacto, output esperado, y criterio de éxito visual (campos en amarillo < 0.70 confianza)
- Patrón de IA de alta relevancia práctica: structured outputs con confidence scoring es un skill directamente transferible a proyectos reales
- Terreno a explorar de alta calidad: preguntas no triviales sobre calibración de confidence, resolución de valores contradictorios y trade-offs entre métodos de forcing
- Extensión bien escalonada: desde batch processing hasta integración con Notion/Confluence, cubre equipos de distinto ritmo
- Reflexión AI con template concreto y preguntas que provocan pensamiento crítico sobre los límites del LLM

### Gaps / Mejoras sugeridas
- AWS está completamente ausente: podría mencionar Amazon Textract (para PDFs con tablas complejas) o Bedrock como alternativa gestionada, aunque sea en el stack opcional
- El criterio de confidence score como auto-reporte del LLM es técnicamente problemático (el modelo puede reportar 0.90 para todo) — el lab lo menciona como pregunta de exploración pero podría sugerir una estrategia alternativa mínima como baseline
