# Criterios del Programa de Labs de IA

> Fuente de verdad del programa. Se actualiza a medida que evoluciona.
> Última actualización: 2026-03-30

---

## Filosofía del Programa

Los labs no enseñan teoría ni preparan para certificaciones. Están diseñados para que equipos pequeños aprendan **haciendo**: construyendo algo real, con herramientas de IA reales, resolviendo problemas que importan hoy.

Hay dos ejes de aprendizaje simultáneos:

1. **Construir con IA** — aprender patrones de GenAI aplicados (RAG, agents, evals, etc.) construyendo un producto funcional
2. **Usar IA para construir** — desarrollar buenos hábitos de uso de IA en el ciclo de desarrollo, y documentar cómo esas prácticas mejoran con el tiempo

El segundo eje es tan importante como el primero. Cada lab es una oportunidad para observar cómo la IA cambia el flujo de trabajo y retroalimentar esas prácticas al resto del programa.

---

## Parámetros del Programa

| Parámetro | Valor |
|---|---|
| **Tamaño del equipo** | 2–3 personas |
| **Duración por lab** | 2–3 semanas |
| **Dedicación semanal** | ~4 horas por semana |
| **Esfuerzo total estimado** | 8–12 horas por equipo por lab |
| **Labs** | Independientes (cualquier orden, equipos distintos pueden hacer labs distintos en paralelo) |
| **Nivel de participantes** | Mixto (no asumir experiencia previa con LLMs) |
| **Resultado mínimo** | Demo funcional + lecciones aprendidas |
| **Presentación** | ~15 minutos por equipo |

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
- Para equipos sin acceso a servicios específicos de AWS: siempre debe existir una alternativa open-source/local (Ollama, Chroma, FAISS, etc.)

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

Cada lab incluye una **Sección de Reflexión AI** que los equipos completan al terminar. Tiene dos formatos:

### Durante la presentación (3-5 min al final de la demo de 15 min)
El equipo responde brevemente:
- ¿Qué herramientas de IA usaron y en qué fases?
- ¿En qué momento la IA fue más útil? ¿En qué momento no ayudó?
- ¿Qué haría diferente el equipo en el próximo lab respecto al uso de IA?

### Documento de lecciones (template incluido en cada lab)
Template estructurado que cada equipo completa y se comparte con todos los equipos del programa. Sirve como input para mejorar el diseño de labs futuros.

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

Estos documentos se consolidan entre ciclos del programa para evolucionar los labs.

---

## Criterios de un Buen Lab

### Criterios de Diseño

- [ ] **Producto objetivo claro** — se construye algo concreto y demostrable, no solo se lee código o se responden preguntas
- [ ] **Alcanzable en 8–12 horas de equipo** — el MVP es completable en 2-3 semanas a ~4 hs/sem
- [ ] **Anclado en un patrón de IA real** — enseña un patrón vigente, no teoría ni prep de certificación
- [ ] **Problema realista** — el escenario es creíble para equipos de software, DevOps o técnicos
- [ ] **Requiere decisiones técnicas** — el equipo toma decisiones de diseño, no solo sigue instrucciones
- [ ] **Comparable entre equipos** — distintos equipos pueden comparar sus resultados y aprendizajes
- [ ] **AWS como opción preferida** — cuando hay servicios recientes de bajo costo que aportan valor al caso
- [ ] **Alternativa open-source siempre disponible** — para cuando AWS no es accesible o el costo no se justifica
- [ ] **Costo AWS mínimo** — los recursos creados no deben exceder ~$5–10 por equipo para el MVP

### Criterios de Evaluación de Cada Lab

- [ ] **Demo funcional** — el producto corre y puede demostrarse en 15 minutos
- [ ] **Métricas de calidad específicas** — al menos 3 métricas medibles en la tabla de evaluación del lab
- [ ] **Lecciones aprendidas documentadas** — el equipo completa el template de Reflexión AI
- [ ] **Decisiones técnicas justificadas** — el equipo puede explicar por qué eligió su stack y su arquitectura

### Anti-criterios

- [ ] No basarse en preguntas de certificación como objetivo de aprendizaje
- [ ] No ser un walkthrough de código sin producto que construir
- [ ] No depender de datos sensibles o de producción
- [ ] No requerir infraestructura costosa o de costo fijo para el MVP
- [ ] No ignorar el eje "AI asiste al equipo mientras hace el lab"
- [ ] No asumir que todos los participantes tienen el mismo nivel

---

## Estructura Estándar de un Lab

Cada lab tiene estas **10 secciones** (la sección 10 es el loop de mejora):

| # | Sección | Descripción |
|---|---|---|
| 1 | **Nombre** | Claro y atractivo |
| 2 | **Skill principal** | Patrón de IA que se explora |
| 3 | **Problema a resolver** | Escenario concreto y realista |
| 4 | **Producto objetivo** | Qué se construye + descripción de la demo de 15 min |
| 5 | **Alcance del MVP** | Qué se logra en la primera fase (semana 1–2) |
| 6 | **Extensión** | Cómo evoluciona si el equipo tiene más tiempo |
| 7 | **Stack sugerido** | Tecnologías con opción AWS + opción open-source |
| 8 | **Criterios de evaluación** | Tabla con métricas medibles |
| 9 | **Riesgos y desafíos** | Dónde los equipos típicamente se traban |
| 10 | **Reflexión AI** | Template de lecciones sobre uso de IA durante el lab |

---

## Alcance de los Labs Actuales

| Lab | Nombre | Skill Principal | Estado |
|---|---|---|---|
| 01 | RAG Debugging | Embeddings, debugging silencioso | ⚠️ Formato tutorial (pre-criterios) |
| 02 | Guardrails Tracing | Guardrail policies, tracing | ⚠️ Formato tutorial (pre-criterios) |
| 03 | Guardrails IAM | IAM governance | ⚠️ Formato tutorial (pre-criterios) |
| 04 | KB Event Sync | Event-driven, RAG sync | ⚠️ Formato tutorial (pre-criterios) |
| 05 | S3 Vectors | Vector search, costo | ⚠️ Formato tutorial (pre-criterios) |
| 06 | Cross-Region Inference | Throttling, throughput | ⚠️ Formato tutorial (pre-criterios) |
| 07 | CodeReviewer AI | LLM workflows + structured output | ✅ Nuevo formato — falta sección 10 |
| 08 | PromptBench | Prompt evaluation / LLM-as-judge | ✅ Nuevo formato — falta sección 10 |
| 09 | AgentOps | AI Agents + tool use | ✅ Nuevo formato — falta sección 10 |
| 10 | DocSense | Structured extraction | ✅ Nuevo formato — falta sección 10 |
| 11 | KnowledgeBot | RAG con evaluación | ✅ Nuevo formato — falta sección 10 |

---

## Historial de Cambios

| Fecha | Cambio |
|---|---|
| 2026-03-30 | Creación inicial. Labs 01-06 identificados como derivados de certificación AWS. Labs 07-11 creados como labs de producto. |
| 2026-03-30 | Criterios afinados: duración 2-3 sem / 4 hs/sem, herramientas Codex+Windsurf+Copilot+Claude, labs independientes, nivel mixto, AWS corporativo bajo costo, loop de mejora = demo + documento. |
