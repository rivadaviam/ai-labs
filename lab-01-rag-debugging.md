# Lab 01: RAG Health Monitor — Detección Automática de Degradación Silenciosa

**Duración del equipo:** 2–3 personas
**MVP:** 2 semanas | **Extensión:** si hay más tiempo
**Nivel:** Intermedio
**Servicios AWS:** Bedrock, Lambda, EventBridge, CloudWatch, OpenSearch Serverless

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

**Demo de 15 minutos:**

1. **(0-3 min)** Mostrar el dashboard con todo en verde: `EmbeddingModelConsistency = 1`, `RetrievalQualityScore = 0.85`
2. **(3-6 min)** Simular el bug: cambiar el model ID en la variable de entorno del Lambda de `v1` a `v2`
3. **(6-10 min)** Esperar la ejecución del EventBridge (o disparar el Lambda manualmente). Mostrar la CloudWatch Alarm disparando con el mensaje: `"Embedding model mismatch: index=titan-v1, query=titan-v2"`
4. **(10-13 min)** Mostrar el gráfico de `RetrievalQualityScore` cayendo de 0.85 a 0.12 en el dashboard
5. **(13-15 min)** Revertir el cambio. Mostrar la alarma volviendo a OK en la siguiente ejecución

---

## 5. Alcance del MVP (2 semanas)

**Semana 1 — Infraestructura y lógica core:**
- Provisionar OpenSearch Serverless o Bedrock Knowledge Base con un corpus de documentos de prueba (mínimo 50 documentos)
- Implementar el Lambda con tres módulos: `embedding_consistency_check`, `canary_runner`, `metrics_publisher`
- Definir un set de 5-10 canary queries con sus respuestas esperadas y scores de similarity umbral
- Publicar las 4 métricas custom a CloudWatch
- EventBridge rule configurada para ejecutar cada 5 minutos

**Semana 2 — Observabilidad y validación:**
- Dashboard de CloudWatch con los 4 widgets principales
- CloudWatch Alarm configurada sobre `EmbeddingModelConsistency` con threshold = 0
- Alarm adicional sobre `RetrievalQualityScore` con threshold < 0.5
- Script de "break" que simula el bug (cambia la env var del Lambda)
- Runbook en Markdown documentando cómo responder a cada alarma

---

## 6. Extensión (si hay más tiempo)

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

## 8. Criterios de evaluación

| Criterio | Métrica mínima de éxito | Peso |
|---|---|---|
| **Detección del bug** | La alarma se dispara en < 5 minutos desde que se introduce el mismatch | 30% |
| **Calidad de métricas** | Las 4 métricas se publican en cada ejecución durante 30 minutos sin errores | 25% |
| **Precisión del score** | `RetrievalQualityScore` ≥ 0.70 en estado normal; ≤ 0.20 con mismatch | 20% |
| **Dashboard legible** | Un observador externo entiende el estado del sistema en < 30 segundos | 15% |
| **Runbook** | Documenta al menos 2 tipos de alarma con pasos concretos de remediación | 10% |

---

## 9. Riesgos y desafíos

- **OpenSearch Serverless tarda en provisionar** — Las collections pueden tardar 5-10 minutos. Reservar tiempo el primer día para provisionar antes de empezar a codear.
- **Costos de Bedrock embeddings con corpus grande** — Si el corpus es grande, el costo de re-indexar puede superar el presupuesto. Usar un corpus de prueba pequeño (50-100 documentos).
- **Canary queries poco representativas** — Si las queries son demasiado genéricas, el similarity score puede ser alto incluso con embeddings incorrectos. Diseñar queries específicas del dominio del corpus.
- **IAM permissions del Lambda** — El Lambda necesita permisos para `bedrock:InvokeModel`, `aoss:APIAccessAll`, y `cloudwatch:PutMetricData`. Configurar el rol antes de empezar a codear.
- **Falsos positivos en la alarma** — Con corpus pequeño o queries mal calibradas, la alarma puede dispararse sin bug real. Ajustar los thresholds durante la semana 1 antes de considerar el sistema estable.

---

## 10. Reflexión AI (completar al terminar el lab)

Cada equipo completa este template y lo comparte con el programa. Alimenta el loop de mejora entre ciclos.

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

### ¿La IA cambió cómo trabajó el equipo (no solo lo aceleró)?
[ ] Sí — describir: ___
[ ] No, solo aceleró tareas existentes

### Recomendación para el próximo equipo
[ej: "Dedicá el primer día a provisionar OpenSearch y verificar que el Lambda puede
escribir métricas a CloudWatch antes de empezar la lógica de detección."]
```
