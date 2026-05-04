# Scorecard Consolidado — AI Labs Program

**Fecha de análisis:** 2026-03-31
**Criterios:** 9 diseño + 6 anti-criterios + 9 estructura = 24 puntos máx.
**Fuente:** [CRITERIA.md](../CRITERIA.md)

---

## Tabla Resumen

| Lab | Nombre | Patrón principal | Diseño | Anti-crit. | Estructura | **Total** |
|---|---|---|---|---|---|---|
| 00 | AI Functions | AI Functions + Agentic flows | 9/9 | 6/6 | 9/9 | **24/24** ✅ |
| 01 | RAG Health Monitor | Observabilidad RAG | 9/9 | 6/6 | 9/9 | **24/24** ✅ |
| 02 | Guardrails Calibration | Evaluación guardrails FP/FN | 9/9 | 6/6 | 9/9 | **24/24** ✅ |
| 03 | AI Governance Scanner | Governance + IAM compliance | 9/9 | 6/6 | 9/9 | **24/24** ✅ |
| 04 | Live KB Sync Pipeline | Event-driven + Knowledge Bases | 9/9 | 6/6 | 9/9 | **24/24** ✅ |
| 05 | Semantic Search Benchmark | Benchmarking vector search | 9/9 | 6/6 | 9/9 | **24/24** ✅ |
| 06 | Resilient LLM Gateway | Resiliencia + cross-region | 9/9 | 6/6 | 9/9 | **24/24** ✅ |
| 07 | CodeReviewer AI | LLM workflows + structured output | 8/9 ⚠️ | 6/6 | 9/9 | **23/24** |
| 08 | PromptBench | Prompt evaluation + LLM-as-judge | 7/9 ⚠️ | 6/6 | 9/9 | **22/24** |
| 09 | AgentOps | AI Agents + tool use (ReAct) | 9/9 | 6/6 | 9/9 | **24/24** ✅ |
| 10 | DocSense | Structured extraction + docs | 8/9 ⚠️ | 6/6 | 9/9 | **23/24** |
| 11 | KnowledgeBot | RAG con evaluación + feedback loop | 8/9 ⚠️ | 6/6 | 9/9 | **23/24** |

**Promedio del programa: 23.5/24 (97.9%)**

---

## Distribución de Scores

| Score | Labs |
|---|---|
| 24/24 ✅ | 00, 01, 02, 03, 04, 05, 06, 09 (8 labs — 67%) |
| 23/24 | 07, 10, 11 (3 labs — 25%) |
| 22/24 | 08 (1 lab — 8%) |

---

## Patrón de Gaps

**El único gap recurrente en el programa es el criterio 8 de diseño: "AWS preferido pero no obligatorio".**

Labs 07, 08, 10, y 11 no mencionan servicios AWS en ningún punto. El gap no es un problema de diseño conceptual — todos son labs de producto con patrones válidos — sino de oportunidad perdida para conectar los patrones enseñados con el ecosistema AWS que los equipos usan en producción.

| Lab | Criterio con gap | Detalles |
|---|---|---|
| 07 CodeReviewer | AWS (⚠️) | No menciona AWS; Ollama en EC2 es opción pero no se orienta hacia servicios AWS |
| 08 PromptBench | AWS (⚠️x2) | Stack 100% local u OpenAI; sin mención de S3, CloudWatch, ni Bedrock |
| 10 DocSense | AWS (⚠️) | Stack 100% local; omite Amazon Textract y Bedrock como alternativas |
| 11 KnowledgeBot | AWS (⚠️) | Stack 100% local; podría mencionar Bedrock embeddings y OpenSearch Serverless |

---

## Fortalezas del Programa

- **Estructura 100% completa:** todos los labs tienen las 9 secciones estándar sin excepción
- **Anti-criterios 100%:** ningún lab viola algún anti-criterio
- **Demos bien definidos:** prácticamente todos tienen un demo de 15 minutos concreto y verificable con criterio de éxito explícito
- **Stack al servicio del concepto:** ningún lab prescribe tecnología innecesariamente
- **Reflexión AI integrada:** todos incluyen el template de reflexión correctamente

---

## Recomendaciones Prioritarias

1. **Labs 07, 08, 10, 11 — agregar mención AWS opcional:** No es necesario hacerlo obligatorio. Con 1-2 líneas en la sección "Stack sugerido" mencionando el servicio AWS equivalente (CodeGuru, S3+CloudWatch, Textract, Bedrock) se completa el criterio sin cambiar el diseño del lab.

2. **Lab 08 — schema de métricas para comparabilidad:** Definir un schema mínimo de output en el reporte HTML para facilitar comparación entre equipos en el show & tell.

3. **Todos los labs — reforzar eje AI-asiste-al-equipo:** La Reflexión AI está bien diseñada pero es opcional. Considerar que la pregunta "¿la IA cambió cómo trabajó el equipo?" sea parte del show & tell mínimo, no solo del write-up escrito.

---

*Scorecards individuales disponibles en `scorecards/lab-XX-scorecard.md`*
