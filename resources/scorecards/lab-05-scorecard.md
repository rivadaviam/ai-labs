# Scorecard — Lab 05: Semantic Search Benchmark — S3 Vectors vs. Alternativas

**Skill principal:** Evaluación de arquitecturas y benchmarking de sistemas AI
**Patrón de IA:** Vector search / semantic search con embeddings
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | `vector-bench.py` con CLI, reporte HTML comparativo, demo de 15 minutos bien especificada con output de ejemplo |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP de 2 semanas bien delimitado: semana 1 corpus + backends, semana 2 benchmark + reporte. Scope explícitamente acotado. |
| 3 | Anclado en patrón de IA real | ✅ | Vector search / RAG es patrón central en producción. S3 Vectors es servicio nuevo con decisión de arquitectura real en juego. |
| 4 | Problema realista | ✅ | Escenario creíble: equipo debe elegir backend de vector search y justificar la decisión ante arquitectura con datos propios, no benchmarks genéricos. |
| 5 | Requiere decisiones técnicas | ✅ | El equipo diseña el protocolo del benchmark (corpus, queries, runs), modela costos, interpreta overlap de resultados y traduce datos en recomendación. |
| 6 | Comparable entre equipos | ✅ | Métricas estandarizadas (latencia p50/p95/p99, overlap top-5, costo estimado). El reporte HTML permite comparación directa entre resultados de distintos equipos. |
| 7 | Stack al servicio del concepto | ✅ | Las herramientas (boto3, ChromaDB, pandas, Jinja2) están justificadas por su rol en el benchmark. No hay tecnología prescripta sin razón. |
| 8 | AWS preferido pero no obligatorio | ✅ | Alternativa open-source completa definida (FAISS + ChromaDB + sentence-transformers, costo $0). AWS es la opción recomendada, no la única. |
| 9 | Costo AWS mínimo | ✅ | "< $1 para indexar ~10k vectores de prueba y correr 500-1000 queries." Muy por debajo del límite de $5–10. |

**Diseño: 9/9 ✅ · 0/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | El objetivo es construir un benchmarking tool real y tomar una decisión de arquitectura, no preparar para ningún examen. |
| 2 | No es walkthrough sin producto | ✅ | Hay un producto concreto: `vector-bench.py` + `report.html`. No es una guía de pasos sin outcome. |
| 3 | No depende de datos sensibles | ✅ | "corpus de documentos técnicos reales o del dataset del lab" — corpus de prueba definido por el equipo, sin requerimiento de datos de producción. |
| 4 | No requiere infra costosa | ✅ | S3 Vectors pago por uso, alternativa local disponible, costo estimado < $1. No hay costo fijo. |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Sección "Reflexión AI" con template específico para este lab, incluyendo advertencia sobre limitaciones de IA con API nueva de S3 Vectors. |
| 6 | No asume nivel homogéneo | ✅ | Nivel "Intermedio" declarado. Sección de extensión progresiva (FAISS, recall con ground truth, modelo interactivo) para equipos más avanzados. |

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

**Score general:** 24/24 (diseño 9/9 + anti-criterios 6/6 + estructura 9/9)

### Fortalezas
- Demo de 15 minutos con output simulado concreto hace el producto completamente tangible antes de que el equipo empiece.
- Alternativa open-source de costo $0 perfectamente especificada, sin degradar el aprendizaje central.
- La sección "Terreno a explorar" es excepcionalmente fuerte: preguntas que conectan la mecánica técnica (percentiles, overlap, diseño de corpus) con la decisión de negocio.
- La advertencia en "Reflexión AI" sobre limitaciones de IA con APIs nuevas (S3 Vectors) es un ejemplo concreto de pensamiento crítico sobre herramientas, no solo celebración del uso de IA.
- Modelo de costos multi-escala (10k / 100k / 1M vectores) enseña una habilidad de arquitectura real, no solo benchmarking de laboratorio.

### Gaps / Mejoras sugeridas
- No hay ningún gap estructural. Como observación opcional: el lab podría sugerir explícitamente un corpus de datos públicos de referencia (ej: documentación de AWS, artículos de Wikipedia técnicos) para que equipos con menos contexto de dominio tengan un punto de partida concreto.
- La tabla de métricas en la demo usa valores de ejemplo (42ms, 87.3%) que podrían marcarse más claramente como ilustrativos para evitar que equipos los tomen como baseline esperado.
