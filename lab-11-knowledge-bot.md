# Lab 11: KnowledgeBot — RAG con Feedback Loop y Calidad Medible

**Duración del equipo:** 2–3 personas
**MVP:** 1 semana | **Extensión:** 1 mes
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

**Demo en 15 minutos:** En el canal `#dev-help` de Slack, escribir `¿por qué usamos PostgreSQL y no MongoDB para los eventos de auditoría?`. El bot responde en < 10 segundos con: la respuesta basada en el ADR relevante, la cita exacta (nombre del documento, sección, fragmento), un score de confianza, y botones de 👍 / 👎. Luego abrir el dashboard (Streamlit o tablas en terminal): preguntas frecuentes que el bot no pudo responder bien, documentos más citados esta semana, score promedio por día. Finalmente, hacer una pregunta que claramente no tiene respuesta en los docs y mostrar que el bot responde "No encontré información suficiente sobre esto" en lugar de alucinar.

---

## 5. Alcance del MVP (1 semana)

- Pipeline de ingestion: procesar carpeta de archivos Markdown/PDF, chunking con overlap configurable, generación de embeddings con modelo local
- Vector store: Chroma o FAISS (local, cero costo, sin setup de servidor)
- Bot de Slack con Bolt SDK Python o CLI interactiva como fallback
- Pipeline RAG completo: query → embed → retrieve top-k chunks → prompt con contexto → respuesta con cita
- Citas de fuente obligatorias: el LLM solo puede citar documentos incluidos en el contexto, nunca inventar referencias
- Detección de "no sé": si el similarity score del retrieval es bajo, el bot admite no tener información suficiente
- Logging en SQLite: cada pregunta + respuesta + documentos recuperados + score de retrieval
- Feedback de usuarios: thumbs up/down en Slack o rating en CLI, guardado en SQLite

---

## 6. Extensión (1 mes)

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

## 8. Criterios de evaluación

| Criterio | Métrica mínima de éxito |
|---|---|
| Precision@3 del retrieval | ≥ 0.70 sobre el set de 30 preguntas de prueba del lab |
| Calidad percibida | 15/20 respuestas reciben thumbs up de los miembros del equipo durante la semana de uso |
| Fidelidad de citas | 90% de respuestas citan un documento real del corpus (verificado manualmente) |
| Latencia | Respuesta end-to-end en < 8 segundos para preguntas típicas |
| Detección de gaps | Identifica correctamente 3 de 5 preguntas plantadas que no tienen respuesta en la documentación |

---

## 9. Riesgos y desafíos

- **Chunking es el error #1 en RAG** — Chunks demasiado pequeños pierden contexto; chunks demasiado grandes reducen la precisión del retrieval. No hay una respuesta universal: hay que experimentar con chunk_size entre 256-1024 tokens y overlap entre 10-20%. Planificar tiempo para este tuning.
- **El LLM alucina citas** — Inventa nombres de documentos o secciones que no existen. Solución: el prompt del sistema debe ser explícito: "Solo podés citar documentos que te proporciono en el contexto. Si la información no está en los documentos provistos, decí que no tenés información suficiente."
- **Embedding model version mismatch** — Si se cambia el modelo de embeddings después de indexar, el vector store queda corrupto y los resultados son basura sin errores explícitos. Guardar el nombre del modelo como metadata del índice y verificarlo al inicializar.
- **Setup de Slack bot consume 1-2 días** — Si el equipo no tiene permisos de workspace admin o no puede crear una app en Slack. Tener siempre implementada la CLI interactiva como fallback para no bloquear el progreso del lab.

---

## 10. Reflexión AI (completar al terminar el lab)

Cada equipo completa este template y lo comparte con el programa. Alimenta el loop de mejora entre ciclos.

```
## Reflexión AI — KnowledgeBot — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| | | |

### Mayor impacto de IA en este lab
[ej: escribir el system prompt anti-alucinaciones, implementar el pipeline de chunking, generar preguntas de prueba para el dataset]

### Momento donde la IA no ayudó o introdujo problemas
[ej: el LLM inventó citas de documentos, sugirió chunk sizes sin tener en cuenta el tipo de documento]

### ¿La IA cambió cómo trabajó el equipo (no solo lo aceleró)?
[ ] Sí — describir: ___
[ ] No, solo aceleró tareas existentes

### Recomendación para el próximo equipo
[ej: "El sistema anti-alucinaciones depende mucho del system prompt — pedile a IA que te ayude a testearlo con adversarial queries desde el inicio"]
```
