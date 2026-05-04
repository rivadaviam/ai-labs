# Scorecard — Lab 09: AgentOps — Agente Autónomo de Diagnóstico de Infraestructura

**Skill principal:** AI Agents + Tool Use
**Patrón de IA:** ReAct loop (Reason + Act) con function calling y herramientas de diagnóstico
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | CLI interactiva `agentops diagnose` con demo de 15 minutos completamente definido, incluyendo output esperado |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP usa datos simulados, stack acotado (Python + Typer + Rich), 6–8 tools con datos hardcodeados |
| 3 | Anclado en patrón de IA real | ✅ | ReAct loop, function calling, límites de pasos, razonamiento visible — patrones centrales de agentes en producción |
| 4 | Problema realista | ✅ | Diagnóstico de incidentes a las 2am, runbooks desactualizados, múltiples sistemas — escenario de ingeniería muy reconocible |
| 5 | Requiere decisiones técnicas | ✅ | El equipo decide diseño del system prompt, granularidad de tools, cuándo cortar el loop, qué incluir en el contexto del agente |
| 6 | Comparable entre equipos | ✅ | Mismo escenario de diagnóstico permite comparar calidad del razonamiento, cantidad de tool calls, precisión del reporte final |
| 7 | Stack al servicio del concepto | ✅ | Se recomienda implementar el loop ReAct manualmente antes de usar frameworks — el stack refuerza el aprendizaje |
| 8 | AWS preferido pero no obligatorio | ✅ | Stack principal es local (Ollama) con opciones cloud de bajo costo; no hay dependencia de AWS pero tampoco se ignora |
| 9 | Costo AWS mínimo | ✅ | MVP corre completamente local con Ollama o vía Groq gratuito; CloudWatch y RDS son extensiones opcionales |

**Diseño: 9/9 ✅ · 0/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | Foco en construir un agente funcional, no en conocer definiciones o conceptos de examen |
| 2 | No es walkthrough sin producto | ✅ | Producto concreto y demostrable; se dice explícitamente "demo en 15 minutos" con output esperado |
| 3 | No depende de datos sensibles | ✅ | Tools explícitamente diseñadas con "datos simulados realistas" — no hay dependencia de logs o DB de producción |
| 4 | No requiere infra costosa | ✅ | Ollama local + SQLite + Typer; todo el MVP corre sin costo |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Reflexión AI incluye prompts específicos: "diseñar el system prompt del agente, generar datos simulados, debuggear loops" |
| 6 | No asume nivel homogéneo | ✅ | MVP manual para aprender el mecanismo + extensiones con frameworks para quienes van más rápido; sección Terreno a explorar incluye preguntas de distintos niveles de profundidad |

**Anti-criterios: 6/6 pasan**

---

## Estructura del Lab

| # | Sección | Estado |
|---|---|---|
| 1 | Nombre | ✅ |
| 2 | Skill principal | ✅ |
| 3 | Problema a resolver | ✅ |
| 4 | Producto objetivo | ✅ |
| 5 | Alcance del MVP | ✅ |
| 6 | Extensión | ✅ |
| 7 | Stack sugerido | ✅ |
| 8 | Terreno a explorar | ✅ |
| 9 | Reflexión AI | ✅ |

**Estructura: 9/9 secciones presentes**

---

## Resumen

**Score general: 24/24** (diseño 9/9 + anti-criterios 6/6 + estructura 9/9)

### Fortalezas
- El demo de 15 minutos está excepcionalmente bien definido: comando exacto, trace visible, output esperado con causa probable y evidencia — ningún otro lab tiene un criterio de demo tan concreto
- La recomendación de implementar el loop ReAct manualmente antes de usar frameworks es pedagógicamente sólida y diferencia este lab de un tutorial de LangChain
- Los datos simulados están explícitamente justificados como herramienta de aprendizaje, no como atajo; hay una pregunta en Terreno a explorar que lo interroga directamente
- El Terreno a explorar tiene preguntas de alta calidad que cubren fallas reales de agentes (loops, alucinaciones de tool results, legibilidad del trace)

### Gaps / Mejoras sugeridas
- No hay criterio de éxito cuantitativo para el MVP más allá del demo narrativo; podría ayudar definir qué constituye un "buen" reporte de causa raíz
- La sección de extensión incluye "runbook automático con ejecución de remediación" que introduce riesgo operacional significativo; valdría una nota sobre los controles de seguridad mínimos
- No hay orientación sobre cómo evaluar la calidad del razonamiento del agente entre modelos distintos, aunque el Terreno a explorar lo menciona como pregunta abierta
