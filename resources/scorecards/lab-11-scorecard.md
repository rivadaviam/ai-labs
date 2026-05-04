# Scorecard — Lab 11: KnowledgeBot

**Skill principal:** RAG (Retrieval-Augmented Generation) con evaluación
**Patrón de IA:** RAG con feedback loop y métricas de calidad de retrieval
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | "Bot de Slack… que responde preguntas sobre la documentación interna con citas de fuente exactas, y registra cada interacción para medir calidad y detectar gaps." Demo de 15 minutos con pasos concretos. |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP bien delimitado: ingestion pipeline, Chroma/FAISS local, bot Slack o CLI, RAG pipeline, logging SQLite y thumbs up/down. Stack local sin costo ni configuración de servidor. |
| 3 | Anclado en patrón de IA real | ✅ | RAG con feedback loop, métricas de retrieval (precision@k, MRR), detección de "no sé" y gap detection son patrones activos en sistemas de producción. El lab enseña la capa de evaluación que la mayoría de tutoriales omite. |
| 4 | Problema realista | ✅ | "200+ páginas de documentación técnica interna… developers nuevos hacen las mismas preguntas repetidamente." Escenario cotidiano en equipos de software con Confluence/Drive/Markdown. |
| 5 | Requiere decisiones técnicas | ✅ | Chunk size y overlap configurable, elección de modelo de embeddings, diseño del system prompt anti-alucinaciones, umbral de similarity para "no sé", estrategia de logging. El terreno a explorar formaliza estas decisiones. |
| 6 | Comparable entre equipos | ✅ | Métricas objetivas compartidas: precision@k, MRR, score promedio de retrieval, thumbs up/down ratio. Equipos pueden comparar con el mismo conjunto de preguntas de prueba sobre sus propios corpora. |
| 7 | Stack al servicio del concepto | ✅ | Ollama + Chroma + SQLite son mínimos y justificados. Slack Bolt se prescribe porque el bot de Slack es el producto; tiene fallback CLI. Alternativas cloud están listadas como opciones, no requerimientos. |
| 8 | AWS preferido pero no obligatorio | ⚠️ | No se menciona AWS en ningún punto del lab. El stack es 100% local/open-source con alternativas cloud genéricas (OpenAI, Pinecone, Cohere). Podría mencionar Bedrock embeddings o OpenSearch como opción AWS sin obligarlos. |
| 9 | Costo AWS mínimo | ✅ | Stack principal es local (Ollama + Chroma + SQLite). Alternativa cloud mínima: "~$1-2 para todo un mes de uso de demo". Costo muy por debajo del umbral de $5-10. |

**Diseño: 8/9 ✅ · 1/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | Objetivo es construir un producto funcional con métricas propias. No hay referencia a certificaciones ni preguntas de examen. |
| 2 | No es walkthrough sin producto | ✅ | El producto es un bot desplegable con dashboard de métricas. La demo de 15 minutos define exactamente qué debe mostrar el sistema funcionando. |
| 3 | No depende de datos sensibles | ✅ | Usa documentación interna propia del equipo (ADRs, runbooks, guías). El lab no requiere datos de producción ni PII. El corpus es de prueba elegido por el equipo. |
| 4 | No requiere infra costosa | ✅ | Stack local-first con Ollama + Chroma + SQLite. Sin servidores, sin costo fijo. Las alternativas cloud son optativas y de bajo costo. |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Sección "Reflexión AI" con template explícito: herramientas usadas, mayor impacto, momentos donde la IA no ayudó, y recomendaciones específicas al dominio RAG. |
| 6 | No asume nivel homogéneo | ✅ | Nivel declarado "Intermedio–Avanzado". MVP bien delimitado para el nivel base; extensión ofrece re-ranking con cross-encoder, métricas avanzadas y multi-fuente para quienes avanzan más rápido. |

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
- Producto concreto con demo de 15 minutos bien definida, incluyendo el caso negativo (bot admite no saber).
- Enseña la capa de evaluación de RAG (precision@k, MRR, feedback loop) que la mayoría de tutoriales omite — diferenciación real respecto a labs genéricos de RAG.
- Stack local-first elimina fricción de setup y costo, con alternativas cloud bien documentadas.
- Terreno a explorar es de alta calidad: preguntas que llevan a decisiones reales (chunk size, cambio de modelo de embeddings, feedback implícito vs. métricas automáticas).
- Sección Reflexión AI tiene template concreto con ejemplos del dominio específico del lab.

### Gaps / Mejoras sugeridas
- AWS no está contemplado en ningún punto. Se podría mencionar Amazon Bedrock (embeddings con Titan Embed v2 o Cohere en Bedrock) y OpenSearch Serverless como opción para equipos que ya trabajan en AWS, sin hacerlo obligatorio.
- Podría indicar explícitamente cómo los equipos comparan resultados entre sí (ej: dataset de preguntas compartidas de prueba) para reforzar la comparabilidad.
