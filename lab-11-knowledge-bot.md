# Lab 11: KnowledgeBot — RAG con Feedback Loop y Calidad Medible

**Equipo:** 2–3 personas
**Nivel:** Intermedio–Avanzado

---

## 1. Nombre del lab

**KnowledgeBot** — Bot de Slack o CLI que responde preguntas sobre documentación interna con RAG, métricas de calidad, y mejora continua

---

## 2. Skill principal

**RAG (Retrieval-Augmented Generation) con evaluación** — No solo implementar RAG, sino medir su calidad con métricas de retrieval (precision@k, MRR), detectar gaps de documentación, y construir el feedback loop que la mayoría de tutoriales omite.

---

## 3. Problema a resolver

El equipo tiene 200+ páginas de documentación técnica interna (ADRs, runbooks, guías de onboarding, API specs) dispersas en Confluence, carpetas de Google Drive, o repositorios de Markdown. Los developers nuevos hacen las mismas preguntas repetidamente. Los veteranos no recuerdan qué decisiones de arquitectura se tomaron hace 8 meses ni por qué. Buscar en Confluence es frustrante y los resultados son irrelevantes. No hay forma de saber si el knowledge base está completo o tiene gaps.

---

## 4. Producto objetivo

**Un bot de Slack** (o CLI interactiva si no hay workspace disponible) que responde preguntas sobre la documentación interna con citas de fuente exactas, y registra cada interacción para medir calidad y detectar gaps.

**Validaciones sugeridas:**

- Hacer una pregunta cuya respuesta está claramente en la documentación y verificar que el bot responde con cita exacta de la fuente
- Hacer una pregunta que no tiene respuesta en los docs y verificar que el bot admite no tener información en lugar de alucinar
- Verificar que el feedback (thumbs up/down) se registra correctamente en SQLite
- Revisar los logs: verificar que cada interacción guarda la pregunta, respuesta, documentos recuperados, y score de retrieval
- Probar con al menos 5 preguntas variadas para evaluar la calidad general del retrieval

*En el show & tell, el equipo muestra las validaciones que corrió y centra la conversación en decisiones tomadas, problemas encontrados, y lecciones aprendidas.*

---

## 5. Alcance del MVP

**Primera fase — Pipeline RAG funcional:**
- Pipeline de ingestion: procesar carpeta de archivos Markdown/PDF, chunking con overlap configurable, generación de embeddings con modelo local
- Vector store: Chroma o FAISS (local, cero costo, sin setup de servidor)
- Pipeline RAG completo: query → embed → retrieve top-k chunks → prompt con contexto → respuesta con cita
- Citas de fuente obligatorias: el LLM solo puede citar documentos incluidos en el contexto, nunca inventar referencias

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas.

**Segunda fase — Bot, feedback y observabilidad:**
- Bot de Slack con Bolt SDK Python o CLI interactiva como fallback
- Detección de "no sé": si el similarity score del retrieval es bajo, el bot admite no tener información suficiente
- Logging en SQLite: cada pregunta + respuesta + documentos recuperados + score de retrieval
- Feedback de usuarios: thumbs up/down en Slack o rating en CLI, guardado en SQLite

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo.

---

## 6. Extensión

- Métricas de retrieval calculadas sobre el dataset de feedback acumulado: precision@k, MRR, NDCG
- Detección automática de gaps: preguntas frecuentes con score bajo → auto-crear issues en Jira/Linear con título "Documentar: [pregunta frecuente]"
- Re-ranking con cross-encoder (sentence-transformers) para mejorar relevancia antes de enviar al LLM
- Dashboard Streamlit con: calidad por semana, documentos más útiles, evolución de métricas, preguntas sin respuesta agrupadas por tema
- Soporte multi-fuente: ingestar desde Confluence API, GitHub repos, y Google Drive además de archivos locales

---

## 7. Stack sugerido

**Gratis / open-source:**
- Ollama + `nomic-embed-text` para embeddings (local, muy buena calidad para documentación técnica)
- Ollama + Llama 3.1 8B para generación de respuestas
- Chroma DB (local, sin servidor requerido)
- Slack Bolt SDK para Python (requiere workspace de Slack, gratis para crear uno de prueba)
- sentence-transformers `cross-encoder/ms-marco-MiniLM-L-6-v2` para reranking
- SQLite para logging y feedback

**Alternativas cloud:**
- OpenAI `text-embedding-3-small` + GPT-4o mini (~$1-2 para todo un mes de uso de demo)
- Pinecone free tier (100k vectors) como alternativa a Chroma
- Cohere Embed v3 (excelente para documentación técnica en inglés/español)

---

## 8. Terreno a explorar

- ¿Cómo afecta el chunk_size y el overlap a la precisión del retrieval? ¿Por qué no hay una respuesta universal y cómo se encuentra el punto óptimo para un corpus específico?
- ¿Qué es Precision@k y por qué es una métrica útil para evaluar retrieval? ¿Cuáles son sus limitaciones?
- ¿Por qué alucina citas un LLM en un sistema RAG? ¿Cómo el system prompt puede reducir este comportamiento?
- ¿Qué pasa cuando el modelo de embeddings cambia después de que el índice está construido? ¿Cómo se detecta y cómo se previene?
- ¿Cómo se diseña un sistema RAG que reconozca cuando no tiene información suficiente vs. cuando tiene información parcial?
- ¿Cuál es la diferencia práctica entre feedback implícito (thumbs up/down) y métricas automáticas para mejorar un RAG?
- ¿Qué hace que una pregunta no tenga respuesta en la documentación aunque el tema esté relacionado?
- ¿La IA ayudó a diseñar el system prompt anti-alucinaciones del bot, o el equipo tuvo que iterarlo manualmente hasta que dejara de inventar citas?
- ¿En qué parte del lab la IA ahorró más tiempo: pipeline de ingestion, diseño del prompt RAG, integración con Slack, o construcción del logging?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — KnowledgeBot — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Codex / Copilot | Implementar el pipeline de ingestion y el bot de Slack | |
| Claude / ChatGPT | Diseñar el system prompt anti-alucinaciones y generar preguntas de prueba | |
| Windsurf / Cursor | Construir el pipeline RAG y el sistema de logging | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en escribir el system prompt anti-alucinaciones, en implementar el
pipeline de chunking, en integrar con Slack, o en generar preguntas de prueba para
el dataset?]

### Momento donde la IA no ayudó o introdujo problemas
[¿El LLM inventó citas de documentos a pesar del system prompt? ¿La IA sugirió chunk
sizes sin tener en cuenta el tipo de documento? ¿El código de integración con Slack
usaba una versión obsoleta del SDK?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "El sistema anti-alucinaciones depende mucho del system prompt — pedile a IA que
te ayude a testearlo con adversarial queries desde el inicio. El chunking es crítico:
probá distintos tamaños y medid el impacto en retrieval antes de avanzar."]
```
