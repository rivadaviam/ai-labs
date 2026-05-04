# Scorecard — Lab 04: Live Knowledge Base Sync Pipeline

**Skill principal:** Event-driven architecture + AWS Bedrock Knowledge Bases
**Patrón de IA:** Retrieval-Augmented Generation (RAG) con pipeline de ingesta event-driven
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | Stack CDK/SAM deployable + test harness `test_sync.py` con salida de latencia medida. Demo de 15 minutos completamente especificada con outputs esperados. |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP dividido en 2 semanas claras: semana 1 infraestructura base, semana 2 pipeline completo + test harness. Scope bien delimitado con sección "Fuera del MVP". |
| 3 | Anclado en patrón de IA real | ✅ | Enseña event-driven sync para Knowledge Bases, patrón operacional crítico en sistemas RAG en producción. Usa APIs reales de Bedrock KB (`StartIngestionJob`, `DeleteKnowledgeBaseDocuments`). |
| 4 | Problema realista | ✅ | "AI assistant que responde con información obsoleta o cita documentos que ya no existen" — escenario directamente reconocible para equipos con AI interno sobre documentos. |
| 5 | Requiere decisiones técnicas | ✅ | El equipo debe decidir estrategia de batching, manejo de errores en DLQ, lógica de polling del test harness, y cómo observar fallos silenciosos del ingestion job. |
| 6 | Comparable entre equipos | ✅ | El test harness produce métricas concretas (latencia de insert y delete en segundos) que permiten comparación directa entre equipos y entre runs. |
| 7 | Stack al servicio del concepto | ✅ | Cada servicio tiene un rol claro justificado por el patrón (S3 como fuente, SQS como buffer con garantías, Lambda como procesador). Se ofrece alternativa open-source con misma estructura conceptual. |
| 8 | AWS preferido pero no obligatorio | ✅ | Stack AWS especificado como "recomendado" con alternativa open-source explícita (LlamaIndex + ChromaDB + watchdog + sentence-transformers) que preserva el aprendizaje central. |
| 9 | Costo AWS mínimo | ✅ | "Costo estimado: < $2 por equipo para el lab completo." Uso de Lambda + SQS + S3 + Bedrock Titan Embed con volumen bajo es coherente con esa estimación. |

**Diseño: 9/9 ✅ · 0/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | El objetivo es construir y medir un pipeline funcional, no estudiar servicios AWS para un examen. |
| 2 | No es walkthrough sin producto | ✅ | El producto es concreto y demostrable: stack deployable + test harness con métricas de latencia reales. |
| 3 | No depende de datos sensibles | ✅ | El ejemplo usa `policy_2026.md` como documento de prueba generado por el equipo. No requiere datos de producción. |
| 4 | No requiere infra costosa | ✅ | Estimación explícita de < $2. Todos los servicios son serverless o de consumo mínimo. |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Sección "Reflexión AI" incluye template detallado con herramientas, momentos de alto/bajo impacto, y recomendación al próximo equipo. Ejemplos concretos de uso de Copilot, Claude y Windsurf. |
| 6 | No asume nivel homogéneo | ✅ | Nivel marcado como "Intermedio", extensiones opcionales para equipos que avanzan más rápido, y alternativa open-source para quienes no tienen acceso a AWS. |

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

**Score general: 24/24** (diseño 9/9 + anti-criterios 6/6 + estructura 9/9)

### Fortalezas
- El test harness con métricas de latencia medibles es un diferenciador fuerte: hace el aprendizaje concreto y comparable entre equipos.
- La demo de 15 minutos está completamente especificada con outputs esperados, lo que elimina ambigüedad sobre qué constituye "done".
- La alternativa open-source es genuinamente equivalente en estructura conceptual, no un fallback degradado.
- La sección "Reflexión AI" incluye ejemplos de errores reales que los modelos suelen cometer con la API de Bedrock KB, lo que es útil y honesto.
- El terreno a explorar plantea preguntas de diseño genuinas (delete vs insert, fallos silenciosos, consistencia eventual) que promueven pensamiento crítico.

### Gaps / Mejoras sugeridas
- La sección de Reflexión AI está marcada como "opcional" — considerar si debería ser obligatoria dado que el eje AI-asiste-al-equipo es un criterio del programa.
- No se menciona cómo manejar el caso de documentos duplicados o actualizaciones parciales en S3, que puede surgir en la práctica y generar confusión.
- Podría beneficiarse de una nota sobre los límites de la API `IngestKnowledgeBaseDocuments` (tamaño máximo por lote, rate limits) para que los equipos no se topen con errores inesperados.
