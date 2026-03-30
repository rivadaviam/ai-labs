# AI Labs — Programa Práctico de IA para Equipos de Desarrollo

Labs de producto para equipos de 2–3 personas. Cada lab construye algo real, demostrable, y anclado en un patrón de IA vigente. El programa cubre tanto el **uso de IA para construir** como el **aprendizaje de patrones de GenAI** aplicados a problemas de desarrollo real.

📋 **Criterios y filosofía del programa:** [CRITERIA.md](CRITERIA.md)

---

## Formato de los Labs

| Parámetro | Valor |
|---|---|
| Equipos | 2–3 personas |
| Duración por lab | 2–3 semanas |
| Dedicación | ~4 horas/semana (~8-12 hs totales) |
| Entregable | Demo funcional de 15 min + lecciones aprendidas |
| Herramientas AI | Copilot, Codex, Windsurf (corporativo) + Claude (personal) |

Cada lab tiene 10 secciones: nombre, skill, problema, producto, MVP, extensión, stack, criterios de evaluación, riesgos, y reflexión AI.

---

## Labs Disponibles

### Introductorios

| # | Lab | Skill | Artefacto | Tiempo |
|---|-----|-------|-----------|--------|
| 00 | [AI Functions — Tu Primer Prototipo Agentico](lab-00-AI-functions.md) | AI Functions + Post-conditions + Agentic flows | Script / notebook | 2–3 sem |

### Patrones de GenAI con AWS Bedrock

| # | Lab | Skill | Artefacto AWS | Tiempo |
|---|-----|-------|--------------|--------|
| 01 | [RAG Health Monitor](lab-01-rag-debugging.md) | Observabilidad de pipelines RAG | Lambda + CloudWatch dashboard | 2 sem |
| 02 | [Guardrails Calibration Tool](lab-02-guardrails-tracing.md) | Evaluación de guardrails (FP/FN) | CLI + reporte HTML | 2 sem |
| 03 | [AI Governance Scanner](lab-03-guardrails-iam.md) | AI governance + IAM compliance | CLI audit + fixes listos | 2 sem |
| 04 | [Live KB Sync Pipeline](lab-04-kb-sync-eventdriven.md) | Event-driven + Knowledge Bases | CDK stack + test harness | 2 sem |
| 05 | [Semantic Search Benchmark](lab-05-vector-search-s3.md) | Benchmarking de vector search | Script + reporte comparativo | 2 sem |
| 06 | [Resilient LLM Gateway](lab-06-cross-region-inference.md) | Resiliencia + cross-region inference | FastAPI proxy + métricas | 2 sem |

### Patrones de GenAI agnósticos de cloud

| # | Lab | Skill | Artefacto | Tiempo |
|---|-----|-------|-----------|--------|
| 07 | [CodeReviewer AI](lab-07-code-reviewer-ai.md) | LLM workflows + structured output | GitHub Action | 2 sem |
| 08 | [PromptBench](lab-08-prompt-bench.md) | Prompt evaluation / LLM-as-judge | CLI + reporte HTML | 2 sem |
| 09 | [AgentOps](lab-09-agentops.md) | AI Agents + tool use (ReAct) | CLI interactiva | 2 sem |
| 10 | [DocSense](lab-10-docsense.md) | Structured extraction + docs | CLI + API REST | 2 sem |
| 11 | [KnowledgeBot](lab-11-knowledge-bot.md) | RAG con evaluación y feedback loop | Slack bot / CLI | 2 sem |

---

## Cómo Usar estos Labs

Cada lab incluye:
- **Problema** — Escenario realista de un equipo de desarrollo
- **Producto objetivo** — Qué se construye exactamente + cómo es la demo de 15 min
- **MVP (2 semanas)** — Qué se espera lograr con la dedicación del programa
- **Stack** — Siempre incluye opción AWS y opción open-source/local
- **Criterios de evaluación** — Tabla con métricas medibles
- **Reflexión AI** — Template para documentar cómo usaron IA durante el lab

Los labs son **independientes**: cualquier equipo puede hacer cualquier lab en cualquier orden.

---

## Sugerencias de Ruta

### Para empezar desde cero con IA
→ **Lab 00** (AI Functions) como punto de entrada, luego cualquier lab de patrones

### Track AWS / Bedrock
→ Labs **01 → 04 → 05** (RAG completo: health, sync, search)
→ Labs **02 → 03** (Seguridad y governance)
→ Lab **06** (Resiliencia en producción)

### Track agnóstico de cloud
→ Labs **07 → 08** (LLM workflows + evaluación)
→ Labs **09 → 11** (Agentes + RAG avanzado)
→ Lab **10** (Extracción de documentos)

### Por tipo de problema
| Quiero aprender... | Labs sugeridos |
|---|---|
| Cómo monitorear y debuggear AI en producción | 01, 02 |
| Cómo gobernar el uso de IA en la empresa | 03, 02 |
| Cómo mantener documentación IA actualizada | 04, 11 |
| Cómo elegir y comparar servicios de vector search | 05 |
| Cómo hacer la app más resiliente ante throttling | 06 |
| Cómo automatizar code review con IA | 07 |
| Cómo evaluar y mejorar prompts sistemáticamente | 08 |
| Cómo construir un agente de diagnóstico | 09 |
| Cómo extraer datos de documentos no estructurados | 10 |
| Cómo construir un bot de documentación interna | 11 |

---

## Nivel Requerido

Los labs requieren:
- Python y terminal básico
- Conceptos de cloud (S3, funciones serverless) para los labs AWS
- Curiosidad y disposición a experimentar — no se requiere experiencia previa con LLMs

No requieren experiencia en ML ni en matemáticas de vectores.
