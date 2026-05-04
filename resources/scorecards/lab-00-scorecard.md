# Scorecard — Lab 00: AI Functions — Tu Primer Prototipo Agentico

**Skill principal:** AI Functions + Structured Outputs + Agentic Flows
**Patrón de IA:** Agentic pipeline con post-conditions y autocorrección automática
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | "Un script Python o notebook que implementa un pipeline agentico con al menos 2 AI Functions... evidencia visible de que una post-condition atrapó al menos un output incorrecto" |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP dividido en dos fases concretas: primera AI Function funcionando + pipeline completo; scope acotado y progresivo |
| 3 | Anclado en patrón de IA real | ✅ | AI Functions + post-conditions + autocorrección son patrones vigentes y aplicados en producción; usa `strands-ai-functions` como biblioteca concreta |
| 4 | Problema realista | ✅ | Tres escenarios creíbles (Log Intelligence, Meeting Intelligence, Code Quality Reporter) más opción de proponer escenario propio del equipo |
| 5 | Requiere decisiones técnicas | ✅ | El equipo elige escenario, diseña post-conditions (estrictas vs. laxas es una pregunta explícita), decide cuándo paralelizar, elige modelo |
| 6 | Comparable entre equipos | ✅ | Demo estructurada de 15 minutos con formato definido; evidencia de autocorrecciones documentada; métricas de retry como punto de comparación |
| 7 | Stack al servicio del concepto | ✅ | Stack mínimo (Python + strands-ai-functions + cualquier modelo); tecnología elegida para ilustrar el patrón, no como fin en sí mismo |
| 8 | AWS preferido pero no obligatorio | ✅ | Tres opciones explícitas: API externa, Amazon Bedrock, y Ollama local; AWS presentado como "opción corporativa", no requerido |
| 9 | Costo AWS mínimo | ✅ | "Costo < $1 para el lab completo" con Bedrock Claude Haiku o Titan; alternativa local costo $0 |

**Diseño: 9/9 ✅ · 0/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | El objetivo es construir un pipeline funcional con autocorrección observable, no responder preguntas de examen |
| 2 | No es walkthrough sin producto | ✅ | Hay un producto concreto: script/notebook ejecutable + demo de 15 minutos con output verificable |
| 3 | No depende de datos sensibles | ✅ | Escenarios usan "datos sintéticos o anonimizados" explícitamente; escenarios A y B mencionan datos reales anonimizados como opción secundaria |
| 4 | No requiere infra costosa | ✅ | Opción $0 con Ollama; opción <$1 con Bedrock; sin infraestructura de costo fijo |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Sección de Reflexión AI con template explícito que pregunta cómo las herramientas de IA asistieron al equipo durante la construcción del lab |
| 6 | No asume nivel homogéneo | ✅ | Nivel introductorio con MVP en dos fases progresivas; extensiones opcionales para equipos más avanzados; permite elegir escenario propio |

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
- Producto objetivo excepcionalmente concreto: el script de demo muestra exactamente qué se va a ver en pantalla, incluyendo el log de autocorrección con intentos numerados
- Tres escenarios reales bien elegidos que cubren casos comunes en equipos de software (logs, reuniones, código), más la opción de traer el propio
- Estructura de dos fases con hitos claros que permite a equipos de distinto ritmo llegar al MVP sin quedar bloqueados
- Las preguntas del "Terreno a explorar" son genuinamente difíciles y empujan al equipo a pensar en diseño, no solo en implementación
- El eje AI-asiste-al-equipo está integrado naturalmente en el template de reflexión, no agregado como afterthought

### Gaps / Mejoras sugeridas
- El lab menciona `strands-ai-functions` como biblioteca central pero no hay referencia a dónde encontrar el repositorio o su documentación; un equipo nuevo puede quedar bloqueado en el setup
- La opción de Ollama como alternativa local costo $0 es valiosa, pero no hay advertencia sobre los requisitos de hardware (RAM mínima para correr llama3.1 o mistral localmente)
- El template de Reflexión AI está marcado como "opcional", lo que puede resultar en que la mayoría de equipos lo salteen; podría ser más efectivo integrar al menos 2-3 preguntas clave como parte del entregable de la demo
