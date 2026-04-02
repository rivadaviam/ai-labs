# Lab 01: RAG Health Monitor — Detección Automática de Degradación Silenciosa

**Equipo:** 2–3 personas
**Nivel:** Intermedio

---

## 1. Nombre del lab

**RAG Health Monitor** — Detección automática de degradación silenciosa en pipelines RAG

---

## 2. Skill principal

**Observabilidad de sistemas AI** — Instrumentación, métricas custom, y monitoreo proactivo de pipelines de Retrieval-Augmented Generation.

---

## 3. Problema a resolver

Los pipelines RAG fallan de maneras silenciosas. Un cambio aparentemente menor — como una actualización de SDK que cambia el modelo de embeddings de `amazon.titan-embed-text-v1` a `amazon.titan-embed-text-v2:0` — puede hacer que el sistema devuelva "no relevant information found" sin generar ningún error en los logs. El modelo de embeddings usado para indexar documentos y el usado para transformar las queries deben ser idénticos: si divergen, los vectores son matemáticamente incomparables y la retrieval colapsa.

Este tipo de bug es especialmente peligroso porque:
- CloudWatch no registra errores (el pipeline técnicamente "funciona")
- Los usuarios reciben respuestas vacías o genéricas sin entender por qué
- El problema puede persistir días o semanas hasta que alguien lo escala manualmente

El equipo construirá la herramienta que hubiera detectado este problema en minutos, no en días.

---

## 4. Producto objetivo

Un sistema de monitoreo automatizado compuesto por:
- Un **AWS Lambda** que ejecuta checks de salud del RAG pipeline
- Una **EventBridge scheduled rule** que dispara el Lambda cada 5 minutos
- **Métricas custom en CloudWatch** publicadas en cada ejecución
- Un **dashboard de CloudWatch** con visualización en tiempo real
- Una **CloudWatch Alarm** que notifica cuando se detecta degradación

Métricas que el sistema publica:

| Métrica | Tipo | Descripción |
|---|---|---|
| `RAG/EmbeddingModelConsistency` | 1/0 | 1 = modelos coinciden, 0 = mismatch |
| `RAG/RetrievalQualityScore` | Float 0-1 | Similarity promedio de canary queries |
| `RAG/CanaryLatencyMs` | Int | Tiempo de respuesta del pipeline completo |
| `RAG/DocumentsRetrieved` | Int | Cantidad de chunks recuperados por canary |

**Validaciones sugeridas:**

- Verificar que el dashboard muestra las 4 métricas correctamente cuando el pipeline está sano
- Simular el bug de embedding mismatch (cambiar el model ID) y verificar que la alarma se dispara
- Verificar que el `RetrievalQualityScore` cae significativamente tras el mismatch
- Revertir el cambio y verificar que la alarma vuelve a OK en la siguiente ejecución
- Revisar los CloudWatch Logs del Lambda para confirmar que la información es suficiente para diagnosticar el problema

*En el show & tell, el equipo muestra las validaciones que corrió y centra la conversación en decisiones tomadas, problemas encontrados, y lecciones aprendidas.*

---

## 5. Alcance del MVP

**Primera fase — Infraestructura y lógica core:**
- Provisionar OpenSearch Serverless o Bedrock Knowledge Base con un corpus de documentos de prueba (mínimo 50 documentos)
- Implementar el Lambda con los checks de salud: consistency de embedding, canary queries, publicación de métricas
- Definir un set de 5-10 canary queries con sus respuestas esperadas y scores de similarity umbral
- Publicar las 4 métricas custom a CloudWatch
- EventBridge rule configurada para ejecución periódica

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas.

**Segunda fase — Observabilidad y validación:**
- Dashboard de CloudWatch con los widgets principales
- CloudWatch Alarms configuradas sobre las métricas críticas
- Script de "break" que simula el bug de embedding mismatch
- Runbook en Markdown documentando cómo responder a cada alarma

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo.

---

## 6. Extensión

- Agregar detección de **index staleness**: comparar la fecha del último documento indexado contra un umbral configurable
- Implementar **canary queries dinámicas**: generar variaciones automáticas con Bedrock para detectar edge cases sin queries hardcodeadas
- Agregar métrica de **semantic drift**: comparar el score promedio de esta semana contra el de la semana anterior para detectar degradación gradual
- Integrar notificaciones a **Slack o SNS** con el detalle del problema y el link al runbook
- **Lambda de auto-remediación** que, al detectar mismatch, publica un mensaje en SQS para que un operador apruebe el rollback

---

## 7. Stack sugerido

**AWS (recomendado):**

| Componente | Servicio |
|---|---|
| Embeddings | Amazon Bedrock — `amazon.titan-embed-text-v2:0` |
| LLM | Amazon Bedrock — Claude Haiku (bajo costo) |
| Vector store | Amazon OpenSearch Serverless (tipo `vectorsearch`) |
| Orquestación | AWS Lambda (Python 3.12) |
| Scheduler | Amazon EventBridge Scheduler (rate: 5 minutos) |
| Observabilidad | Amazon CloudWatch (métricas custom, dashboard, alarms) |
| Config | AWS Systems Manager Parameter Store (model IDs, thresholds) |

Costo estimado: < $2 por equipo para todo el lab.

**Alternativa open-source:**

| Componente | Herramienta |
|---|---|
| Embeddings | Ollama + `nomic-embed-text` |
| LLM | Ollama + `llama3` |
| Vector store | Chroma (modo local) |
| Scheduler | Python con APScheduler o cron del sistema |
| Observabilidad | Prometheus + Grafana (Docker Compose) |

---

## 8. Terreno a explorar

- ¿Qué métricas son realmente útiles para detectar degradación silenciosa de un pipeline RAG? ¿Cuál es la diferencia entre embedding consistency y retrieval quality?
- ¿Qué hace que una canary query sea representativa del dominio? ¿Cómo saber si estás midiendo lo correcto vs. casos triviales que siempre pasan?
- ¿Cómo se calibran los umbrales de alarma para minimizar falsos positivos sin perder detecciones reales?
- ¿Qué pasa cuando el similarity score cae gradualmente vs. de golpe? ¿El sistema de monitoreo detecta ambos casos igual de bien?
- ¿Cómo afecta el tamaño del corpus a los costos y a la viabilidad del monitoreo continuo?
- ¿Qué información necesita un runbook para ser útil en una situación de incidente real vs. uno que no se usa?
- ¿Por qué una alarma puede dispararse sin que haya un bug real? ¿Qué dice eso sobre la calibración del sistema?
- ¿La IA fue útil para diseñar las canary queries y sus umbrales, o generó casos triviales que no detectarían problemas reales?
- ¿En qué tareas del lab la IA ayudó más: infraestructura (CDK/SAM), lógica del Lambda, o diseño del sistema de monitoreo?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — RAG Health Monitor — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Copilot / Windsurf | Escribir el Lambda handler y los módulos de check | |
| Claude | Diseñar las canary queries y los umbrales de similarity | |
| Codex / ChatGPT | Generar el CDK/SAM para la infra | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en escribir el código del Lambda, en diseñar las canary queries,
en configurar CloudWatch, o en otro punto? ¿Cuánto tiempo estimás que ahorró?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA sugirió código incorrecto para la OpenSearch Serverless API? ¿Generó umbrales
de similarity que no tenían sentido para el corpus? ¿Propuso arquitectura con servicios
que superaban el presupuesto?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Dedicá el primer día a provisionar OpenSearch y verificar que el Lambda puede
escribir métricas a CloudWatch antes de empezar la lógica de detección."]
```
