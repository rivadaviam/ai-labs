# AI Labs — Programa Práctico de IA para Equipos de Desarrollo

Labs prácticos para equipos de 2–3 personas. El fin es el aprendizaje — construir algo es el medio. Cada lab está anclado en un patrón de IA vigente y cubre tanto el **uso de IA para construir** como el **aprendizaje de patrones de GenAI** aplicados a problemas de desarrollo real.

📋 **Criterios y filosofía del programa:** [CRITERIA.md](CRITERIA.md)

---

## Formato de los Labs

| Parámetro | Valor |
|---|---|
| Equipos | 2–3 personas |
| Duración por lab | 3 semanas |
| Dedicación | ~4 horas/semana/persona (24–36 hs totales) |
| Cierre | Show & tell informal del proceso, lo construido, y posibles mejoras |
| Herramientas AI | Copilot, Codex, Windsurf (corporativo) + Claude (personal) |

Cada lab tiene 9 secciones: nombre, skill, problema, producto, MVP, extensión, stack, terreno a explorar, y reflexión AI.

---

## Labs Disponibles

| # | Lab | Skill | Artefacto |
|---|-----|-------|-----------|
| 00 | [AI Functions — Tu Primer Prototipo Agentico](lab-00-AI-functions.md) | AI Functions + Agentic flows | Script / notebook |
| 01 | [RAG Health Monitor](lab-01-rag-debugging.md) | Observabilidad de pipelines RAG | Lambda + CloudWatch dashboard |
| 02 | [Guardrails Calibration Tool](lab-02-guardrails-tracing.md) | Evaluación de guardrails (FP/FN) | CLI + reporte HTML |
| 03 | [AI Governance Scanner](lab-03-guardrails-iam.md) | AI governance + IAM compliance | CLI audit + fixes listos |
| 04 | [Live KB Sync Pipeline](lab-04-kb-sync-eventdriven.md) | Event-driven + Knowledge Bases | CDK stack + test harness |
| 05 | [Semantic Search Benchmark](lab-05-vector-search-s3.md) | Benchmarking de vector search | Script + reporte comparativo |
| 06 | [Resilient LLM Gateway](lab-06-cross-region-inference.md) | Resiliencia + cross-region inference | FastAPI proxy + métricas |
| 07 | [CodeReviewer AI](lab-07-code-reviewer-ai.md) | LLM workflows + structured output | GitHub Action |
| 08 | [PromptBench](lab-08-prompt-bench.md) | Prompt evaluation / LLM-as-judge | CLI + reporte HTML |
| 09 | [AgentOps](lab-09-agentops.md) | AI Agents + tool use (ReAct) | CLI interactiva |
| 10 | [DocSense](lab-10-docsense.md) | Structured extraction + docs | CLI + API REST |
| 11 | [KnowledgeBot](lab-11-knowledge-bot.md) | RAG con evaluación y feedback loop | Slack bot / CLI |
| 12 | [SpecFirst — Desarrollo Guiado por Especificación](lab-12-spec-driven.md) | Spec-driven development | Spec + feature + reporte de adherencia |
| 13 | [ContextBridge — MCP: Usar y Crear](lab-13-mcp.md) | Model Context Protocol (MCP) | MCP server propio + demo de integración |
| 14 | [SkillForge — Crear y Usar Agent Skills](lab-14-agent-skills.md) | Agent Skills | 2-3 skills funcionales + demo |
| 15 | [MultiAgent — Orquestación Multi-Agente](lab-15-multi-agent.md) | Orquestación multi-agente | CLI + reporte consolidado |
| 16 | [RedTeam — Seguridad y Red Teaming de IA](lab-16-red-team.md) | AI Security / Red teaming | Chatbot target + CLI redteam + reporte |
| 17 | [VisualQA — IA Multimodal Aplicada al Desarrollo](lab-17-multimodal.md) | IA Multimodal (visión) | CLI + reporte de regresión visual |
| 18 | [MemoryArch — Gestión de Contexto y Memoria](lab-18-memory.md) | Gestión de contexto y memoria | Asistente con memoria + métricas |

---

## Cómo Usar estos Labs

Cada lab incluye:
- **Problema** — Escenario realista de un equipo de desarrollo
- **Producto objetivo** — Qué se puede construir + validaciones sugeridas para probarlo
- **MVP (3 semanas)** — Qué se puede lograr a ~4 hs/sem/persona, con checkpoints de reflexión al cierre de cada fase
- **Stack** — Solo se prescribe si la tecnología es parte del aprendizaje; si no, el equipo elige
- **Terreno a explorar** — Conceptos clave, fenómenos esperables, y preguntas que el lab debería responder — incluyendo preguntas sobre el uso de IA durante la construcción
- **Reflexión AI** — Síntesis obligatoria de los checkpoints de reflexión recogidos durante el lab

Los labs son **independientes**: cualquier equipo puede hacer cualquier lab en cualquier orden.

---

## Sugerencias de Ruta

| Quiero aprender... | Labs sugeridos |
|---|---|
| Mis primeros pasos con IA | 00 |
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
| Cómo desarrollar guiado por especificaciones generadas con IA | 12 |
| Cómo extender un agente de IA con herramientas propias (MCP) | 13 |
| Cómo empaquetar conocimiento del equipo para agentes de IA | 14 |
| Cómo coordinar múltiples agentes de IA para resolver un problema complejo | 15 |
| Cómo testear la seguridad de un sistema de IA antes de lanzarlo | 16 |
| Cómo usar modelos de visión para automatizar revisión visual de UI | 17 |
| Cómo manejar conversaciones largas sin perder contexto | 18 |

---

## Nivel Requerido

Los labs requieren:
- Python y terminal básico
- Conceptos de cloud (S3, funciones serverless) para los labs AWS
- Curiosidad y disposición a experimentar — no se requiere experiencia previa con LLMs

No requieren experiencia en ML ni en matemáticas de vectores.
