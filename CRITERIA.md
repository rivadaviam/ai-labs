# Criterios del Programa de Labs de IA

> Fuente de verdad del programa. Se actualiza a medida que evoluciona.
> Última actualización: 2026-03-31

---

## Filosofía del Programa

Los labs no enseñan teoría ni preparan para certificaciones. Están diseñados para que equipos pequeños aprendan **haciendo**: el fin es el aprendizaje, no el producto. Construir algo es el medio — el proceso de tomar decisiones, encontrar obstáculos, y resolverlos con IA es lo que importa.

Hay dos ejes de aprendizaje simultáneos:

1. **Construir con IA** — aprender patrones de GenAI aplicados (RAG, agents, evals, etc.) construyendo un producto funcional
2. **Usar IA para construir** — desarrollar buenos hábitos de uso de IA en el ciclo de desarrollo, y documentar cómo esas prácticas mejoran con el tiempo

El segundo eje es tan importante como el primero. Cada lab es una oportunidad para observar cómo la IA cambia el flujo de trabajo y retroalimentar esas prácticas al resto del programa.

---

## Parámetros del Programa

| Parámetro | Valor |
|---|---|
| **Tamaño del equipo** | 2–3 personas |
| **Duración por lab** | 3 semanas |
| **Dedicación semanal** | ~4 horas por semana por persona |
| **Esfuerzo total estimado** | 24–36 horas por equipo por lab |
| **Labs** | Independientes (cualquier orden, equipos distintos pueden hacer labs distintos en paralelo) |
| **Nivel de participantes** | Mixto (no asumir experiencia previa con LLMs) |
| **Cierre del lab** | Show & tell informal del proceso de aprendizaje, lo construido, y posibles mejoras |

---

## Herramientas de IA Disponibles

Los equipos eligen las herramientas que prefieren. Herramientas corporativas disponibles:
- **GitHub Copilot** — completado inline en IDE
- **Codex** — generación de código
- **Windsurf** — IDE con agente integrado

Herramientas adicionales que los participantes pueden incorporar por cuenta propia:
- **Claude** (Claude.ai o Claude Code) — recomendado para tareas de diseño, debugging complejo, y generación de código con contexto amplio

**No hay herramienta obligatoria.** Parte del aprendizaje es elegir, probar, y evaluar qué herramientas funcionan mejor para cada tarea. Eso se documenta en el loop de mejora.

---

## Infraestructura

- **AWS corporativo** — los participantes tienen acceso a una cuenta corporativa
- **Uso responsable** — el costo de AWS por lab debe ser mínimo (idealmente < $5–10 por equipo)
- Priorizar servicios con capa gratuita o pricing por uso antes que recursos con costo fijo (ej: S3 Vectors antes que un cluster de OpenSearch)
- AWS es preferido pero no obligatorio. Los labs son válidos con stack cloud-agnostic.
- Para equipos sin acceso a servicios específicos de AWS: alternativas open-source/local son igualmente válidas (Ollama, Chroma, FAISS, etc.)

---

## Objetivos de Aprendizaje

### 1. Patrones de GenAI Actuales y Relevantes
Cada lab está anclado en un patrón vigente que los equipos de desarrollo necesitan manejar hoy:

| Patrón | Labs que lo cubren |
|---|---|
| RAG con evaluación de calidad | Lab 11 |
| AI Agents con tool use (ReAct) | Lab 09 |
| Prompt evaluation / LLM-as-judge | Lab 08 |
| Structured outputs y extracción de documentos | Lab 10 |
| LLM workflows y output estructurado | Lab 07 |
| Guardrails y governance | Labs 02, 03 |
| Embeddings y vector search | Labs 01, 05 |
| Event-driven con Knowledge Bases | Lab 04 |
| Cross-region inference y throttling | Lab 06 |

### 2. Problemas Reales de GenAI
Los escenarios de los labs deben reflejar problemas que los equipos enfrentan en producción:
- Respuestas de baja calidad o irrelevantes en RAG
- Agentes que entran en loops o alucinan resultados de tools
- Prompts que degradan silenciosamente
- Documentos con información que se extrae de forma inconsistente
- Guardrails que generan falsos positivos y bloquean queries legítimas

### 3. Buenas Prácticas de IA en el Ciclo de Desarrollo
Los participantes practican y documentan el uso de IA en distintas fases:

| Fase del ciclo | Prácticas a explorar |
|---|---|
| **Diseño** | Generar opciones de arquitectura, evaluar trade-offs con IA |
| **Código** | Completado inline, generación de boilerplate, refactor asistido |
| **Testing** | Generar test cases, escribir tests desde descripción en lenguaje natural |
| **Debugging** | Diagnosticar errores, interpretar stack traces, proponer fixes |
| **Documentación** | Generar docstrings, READMEs, ADRs desde el código |
| **Code review** | Detectar issues, sugerir mejoras antes del review humano |

---

## El Loop de Mejora

El cierre de cada lab es un **show & tell informal**: el equipo comparte el proceso de aprendizaje, lo que construyó, y posibles mejoras o continuaciones. No hay formato obligatorio ni entregable previo.

La **Sección de Reflexión AI** incluida en cada lab es un template opcional — no es un pre-requisito del show & tell. Si los resultados del equipo son prometedores o generan aprendizajes relevantes para el programa, se puede acordar un write-up estructurado posterior.

El template de reflexión (incluido en cada lab) sirve como punto de partida si el equipo decide documentar formalmente:

```
## Reflexión AI — [Nombre del Lab] — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| [herramienta] | [tarea específica] | [1-5] |

### Mayor impacto de IA en este lab
[Descripción de 2-3 oraciones: qué tarea se aceleró o mejoró significativamente]

### Momento donde la IA no ayudó o introdujo problemas
[Descripción de 2-3 oraciones: qué salió mal, alucinaciones, contexto insuficiente, etc.]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)?
[ ] Sí — describir: ___
[ ] No, solo aceleró tareas existentes

### Recomendación para el próximo equipo que haga este lab
[1-2 oraciones: qué consejo darías sobre cómo usar IA en este lab específico]
```

---

## Criterios de un Buen Lab

### Criterios de Diseño

- [ ] **Producto objetivo claro** — se construye algo concreto y demostrable, no solo se lee código o se responden preguntas
- [ ] **Alcanzable en 24–36 horas de equipo** — el MVP es completable en 3 semanas a ~4 hs/sem/persona
- [ ] **Anclado en un patrón de IA real** — enseña un patrón vigente, no teoría ni prep de certificación
- [ ] **Problema realista** — el escenario es creíble para equipos de software, DevOps o técnicos
- [ ] **Requiere decisiones técnicas** — el equipo toma decisiones de diseño, no solo sigue instrucciones
- [ ] **Comparable entre equipos** — distintos equipos pueden comparar sus resultados y aprendizajes
- [ ] **Stack al servicio del concepto** — solo se prescribe una tecnología específica si aprenderla es parte del objetivo del lab; en otro caso el equipo elige libremente
- [ ] **AWS preferido pero no obligatorio** — cuando hay servicios relevantes de bajo costo que forman parte del aprendizaje
- [ ] **Costo AWS mínimo** — los recursos creados no deben exceder ~$5–10 por equipo para el MVP

### Criterios de Cierre de Cada Lab

- [ ] **Show & tell realizado** — el equipo presentó su proceso de aprendizaje, lo construido (si aplica), y posibles continuaciones
- [ ] **Decisiones técnicas justificadas** — el equipo puede explicar las elecciones que tomó y qué aprendió de ellas
- [ ] **Reflexión AI opcional** — si el equipo decide documentarla, usa el template incluido en el lab

### Anti-criterios

- [ ] No basarse en preguntas de certificación como objetivo de aprendizaje
- [ ] No ser un walkthrough de código sin producto que construir
- [ ] No depender de datos sensibles o de producción
- [ ] No requerir infraestructura costosa o de costo fijo para el MVP
- [ ] No ignorar el eje "AI asiste al equipo mientras hace el lab"
- [ ] No asumir que todos los participantes tienen el mismo nivel

---

## Estructura Estándar de un Lab

Cada lab tiene estas **9 secciones**:

| # | Sección | Descripción |
|---|---|---|
| 1 | **Nombre** | Claro y atractivo |
| 2 | **Skill principal** | Patrón de IA que se explora |
| 3 | **Problema a resolver** | Escenario concreto y realista |
| 4 | **Producto objetivo** | Qué se construye + cómo se muestra en el show & tell |
| 5 | **Alcance del MVP** | Qué se puede lograr en 3 semanas a 4 hs/sem/persona |
| 6 | **Extensión** | Cómo evoluciona si el equipo decide continuar |
| 7 | **Stack sugerido** | Solo se menciona si la tecnología es parte del aprendizaje; si no, el equipo elige |
| 8 | **Terreno a explorar** | Conceptos clave, fenómenos esperables, y preguntas que el lab debería responder |
| 9 | **Reflexión AI** | Template opcional de lecciones sobre uso de IA durante el lab |

---

## Alcance de los Labs Actuales

| Lab | Nombre | Skill Principal |
|---|---|---|
| 00 | AI Functions | AI Functions + Agentic flows |
| 01 | RAG Health Monitor | Observabilidad de pipelines RAG |
| 02 | Guardrails Calibration Tool | Evaluación de guardrails (FP/FN) |
| 03 | AI Governance Scanner | AI governance + IAM compliance |
| 04 | Live KB Sync Pipeline | Event-driven + Knowledge Bases |
| 05 | Semantic Search Benchmark | Benchmarking de vector search |
| 06 | Resilient LLM Gateway | Resiliencia + cross-region inference |
| 07 | CodeReviewer AI | LLM workflows + structured output |
| 08 | PromptBench | Prompt evaluation / LLM-as-judge |
| 09 | AgentOps | AI Agents + tool use (ReAct) |
| 10 | DocSense | Structured extraction + docs |
| 11 | KnowledgeBot | RAG con evaluación y feedback loop |

---

## Historial de Cambios

| Fecha | Cambio |
|---|---|
| 2026-03-30 | Creación inicial. Labs 01-06 identificados como derivados de certificación AWS. Labs 07-11 creados como labs de producto. |
| 2026-03-30 | Criterios afinados: duración 2-3 sem / 4 hs/sem, herramientas Codex+Windsurf+Copilot+Claude, labs independientes, nivel mixto, AWS corporativo bajo costo, loop de mejora = demo + documento. |
| 2026-03-31 | Filosofía reenfocada: el aprendizaje es el fin, construir es el medio. Duración fija 3 semanas. Entregable → show & tell informal. Reflexión AI opcional. Secciones "Criterios de evaluación" + "Riesgos" mergeadas en "Terreno a explorar". Stack prescriptivo solo si la tecnología es el aprendizaje. AWS preferido pero no obligatorio. Distinción de formato entre labs eliminada. |
