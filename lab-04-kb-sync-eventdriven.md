# Lab 04: Live Knowledge Base Sync Pipeline

**Duración del equipo:** 2–3 personas
**MVP:** 2 semanas | **Extensión:** si hay más tiempo
**Nivel:** Intermedio
**Servicios AWS:** Bedrock Knowledge Bases, S3, SQS, Lambda, CloudWatch, CDK/SAM

---

## 1. Nombre del lab

**Live Knowledge Base Sync Pipeline** — Construir y validar un pipeline event-driven que mantiene una Knowledge Base de Bedrock sincronizada automáticamente con los cambios en S3

---

## 2. Skill principal

**Event-driven architecture + AWS Bedrock Knowledge Bases** — Diseño de pipelines asincrónicos con garantías de entrega, integración con APIs de ingesta de Bedrock, y validación de correctness con latencia medida.

---

## 3. Problema a resolver

Una empresa tiene un AI assistant sobre documentos internos. La fuente de verdad vive en S3 y se actualiza varias veces al día: nuevas políticas, documentos revisados, archivos eliminados. Sin embargo, la Knowledge Base se re-indexa manualmente una vez por semana. El resultado es un asistente que responde con información obsoleta o que cita documentos que ya no existen.

El equipo debe resolver esto con un pipeline completamente automático, y demostrarlo con datos: no basta con que el pipeline "funcione" en teoría — el test harness debe medir el tiempo real que tarda la KB en reflejar cada cambio.

---

## 4. Producto objetivo

Un stack deployable (CDK v2 o SAM) que implementa el pipeline completo:

```
S3 (PutObject / DeleteObject)
  → S3 Event Notifications
    → SQS (con Dead Letter Queue)
      → Lambda (procesador de eventos)
        → Bedrock Knowledge Base
          (IngestKnowledgeBaseDocuments / DeleteKnowledgeBaseDocuments)
```

Más un test harness Python (`test_sync.py`) que valida el pipeline end-to-end midiendo latencia de sincronización para inserciones y eliminaciones.

**Demo de 15 minutos:**

1. **(0-3 min)** `cdk deploy LiveKBSyncStack`. Mostrar los outputs: ARN de la KB, SQS URL, nombre del Lambda.
2. **(3-10 min)** Correr el test harness:
   ```
   python test_sync.py --doc policy_2026.md --query "política de vacaciones 2026"

   [1/6] Uploading doc → s3://corp-docs/policy_2026.md
   [2/6] Waiting for KB sync...
     attempt 1 (t=5s):  no result
     attempt 3 (t=28s): no result
     attempt 4 (t=41s): ✅ Found! "Los empleados tienen 20 días..."
   [3/6] INSERT sync latency: 41 seconds

   [4/6] Deleting doc from S3...
   [5/6] Waiting for KB to clear...
     attempt 2 (t=22s): still returning result
     attempt 3 (t=38s): ✅ Clean — no result returned
   [6/6] DELETE sync latency: 38 seconds
   ```
3. **(11-15 min)** Mostrar CloudWatch Logs del Lambda: eventos recibidos desde SQS, job IDs, tiempos. DLQ vacía (ningún mensaje falló).

---

## 5. Alcance del MVP (2 semanas)

| Semana | Foco | Entregables |
|--------|------|-------------|
| **1** | Infraestructura base | Stack CDK/SAM deployable: S3 + Event Notifications, SQS con DLQ, Lambda conectado, KB de Bedrock creada. Lambda puede llamar a `StartIngestionJob` exitosamente. |
| **2** | Pipeline completo + test harness | Lambda maneja eventos de Delete (`DeleteKnowledgeBaseDocuments`). Test harness con polling y medición de latencia. Demo funciona de punta a punta. |

**Fuera del MVP:** documentos > 100 páginas, múltiples data sources, alarmas CloudWatch automáticas.

---

## 6. Extensión (si hay más tiempo)

- **Batch efficiency:** Agrupar múltiples eventos S3 en un solo `StartIngestionJob` con SQS batching. Medir si reduce el número de jobs sin aumentar latencia.
- **Alarma de DLQ:** CloudWatch alarm cuando la DLQ tenga mensajes. Simular un fallo del Lambda y verificar que la alarma se dispara.
- **Reporte de latencia histórica:** El test harness guarda resultados en JSON y genera un gráfico de latencia a lo largo de múltiples runs.
- **Multi-format support:** Agregar soporte para `.pdf` y `.docx` además de `.md` y `.txt`.

---

## 7. Stack sugerido

**AWS (recomendado):**

| Servicio | Rol |
|----------|-----|
| S3 + S3 Event Notifications | Fuente de docs + trigger de eventos |
| SQS + DLQ | Buffer del pipeline con garantías de entrega |
| Lambda (Python 3.12) | Procesa eventos y llama a la API de Bedrock KB |
| Bedrock Knowledge Bases | Indexación y retrieval semántico |
| Bedrock (Titan Embed v2) | Modelo de embeddings para la KB |
| CloudWatch Logs | Observabilidad del Lambda |
| CDK v2 o SAM | IaC para deploy reproducible |

Costo estimado: < $2 por equipo para el lab completo.

**Alternativa open-source:**

| Componente | Herramienta |
|------------|-------------|
| KB local | LlamaIndex + ChromaDB |
| File watcher | Python `watchdog` (monitorea directorio local) |
| API de queries | FastAPI endpoint `/query` |
| Embeddings | `sentence-transformers` (modelo local) |

El test harness es prácticamente idéntico — solo cambia el cliente de ingesta.

---

## 8. Criterios de evaluación

| Criterio | Métrica mínima de éxito | Peso |
|---|---|---|
| **Pipeline funcional** | Stack deploya sin errores. Documento subido a S3 aparece en KB sin intervención manual. | 30% |
| **Latencia medida** | Test harness reporta latencia de INSERT y DELETE con resultados reproducibles (dos runs en el mismo orden de magnitud). | 25% |
| **Correctness del delete** | Cuando un doc es borrado de S3, la KB deja de citarlo en respuestas dentro de un tiempo razonable. | 20% |
| **Observabilidad** | CloudWatch Logs muestran el ciclo de vida de cada evento: recibido, procesado, job iniciado. DLQ visible. | 15% |
| **IaC reproducible** | Otro equipo puede hacer `cdk deploy` sin configuración manual adicional. README con instrucciones. | 10% |

---

## 9. Riesgos y desafíos

- **Latencia inherente de ingestion jobs** — Los `StartIngestionJob` pueden tardar entre 30 segundos y varios minutos. Esto es normal. El test harness debe tener un timeout configurable (default: 5 minutos) para no fallar con documentos grandes.
- **Permisos IAM en entorno corporativo** — El Lambda necesita `bedrock:StartIngestionJob`, `bedrock:DeleteKnowledgeBaseDocuments`, `s3:GetObject`, `sqs:ReceiveMessage`. Iniciar el proceso de permisos en el día 1.
- **Modelo de embeddings no habilitado** — Verificar en Bedrock → Model Access que `amazon.titan-embed-text-v2:0` o `cohere.embed-english-v3` estén activos antes de crear la KB.
- **La KB no es tiempo real** — El pipeline reduce la latencia de "semanal" a "minutos", no a sub-segundo. Este es un resultado válido y valioso que debe comunicarse claramente en la demo.
- **Costo de ingestion jobs con muchos re-runs** — Usar documentos pequeños (< 5 páginas) para las pruebas del test harness.

---

## 10. Reflexión AI (completar al terminar el lab)

Cada equipo completa este template y lo comparte con el programa. Alimenta el loop de mejora entre ciclos.

```
## Reflexión AI — Live KB Sync Pipeline — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Copilot / Codex | Generar el Lambda handler y el test harness | |
| Claude | Debuggear permisos IAM y entender la API de Bedrock KB | |
| Windsurf | Escribir y refactorizar el stack CDK/SAM | |

### Mayor impacto de IA en este lab
[¿La IA generó el stack CDK completo en el primer intento? ¿Ayudó a entender el
modelo de eventos S3 → SQS → Lambda? ¿Diagnosticó un error de permisos IAM rápidamente?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA usó parámetros incorrectos para la versión actual de la API de Bedrock KB?
¿Generó código de CDK para una versión anterior? ¿El test harness tenía una lógica
de polling que no terminaba?]

### ¿La IA cambió cómo trabajó el equipo (no solo lo aceleró)?
[ ] Sí — describir: ___
[ ] No, solo aceleró tareas existentes

### Recomendación para el próximo equipo
[ej: "Pedirle a Claude que explique el modelo de consistencia de Bedrock KB antes de
escribir código ahorra mucho debugging. Copilot es muy útil para el boilerplate del
Lambda pero no conoce bien DeleteKnowledgeBaseDocuments — verificar la docs oficial."]
```
