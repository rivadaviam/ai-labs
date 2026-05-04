# Scorecard — Lab 07: CodeReviewer AI — El Revisor que Nunca Duerme

**Skill principal:** LLM Workflows + Structured Outputs
**Patrón de IA:** Pipeline multi-prompt con output estructurado (JSON) + acción sobre resultados
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | "Un GitHub Action que, al abrir o actualizar un PR, analiza el diff con un LLM y publica comentarios automáticos directamente en las líneas específicas del código" |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP bien acotado: Action funcional + extracción de diff + llamada LLM + publicación de comentarios via GitHub Review API. Sin over-engineering. |
| 3 | Anclado en patrón de IA real | ✅ | Enseña structured outputs, prompt design para output JSON, context window management y LLM-as-code-reviewer — todos patrones vigentes en producción |
| 4 | Problema realista | ✅ | "Los reviewers humanos están ocupados o en otra zona horaria. Los comentarios de revisión son inconsistentes" — escenario cotidiano en equipos de software |
| 5 | Requiere decisiones técnicas | ✅ | Chunking por context window, reducción de alucinaciones de números de línea, elección de modelo local vs. API, diseño del prompt de categorías |
| 6 | Comparable entre equipos | ✅ | Demo estandarizado: 3 bugs plantados (secret, SQL injection, empty catch) + badge "AI Review: N issues found" permiten comparación directa |
| 7 | Stack al servicio del concepto | ✅ | Stack mínimo (Python + PyGithub + httpx); Streamlit solo en extensión opcional. No se prescribe tecnología innecesaria. |
| 8 | AWS preferido pero no obligatorio | ⚠️ | No menciona AWS explícitamente. Ollama en EC2 free tier aparece como opción, pero el lab no orienta hacia servicios AWS. No viola el criterio pero tampoco lo aprovecha. |
| 9 | Costo AWS mínimo | ✅ | "~$0.001/PR" con GPT-4o mini; Ollama local es costo cero. Bien dentro del rango $5–10. |

**Diseño: 8/9 ✅ · 1/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | Construye un producto funcional; no hay preguntas de trivia ni preparación de examen |
| 2 | No es walkthrough sin producto | ✅ | El producto (GitHub Action publicando comentarios en PRs reales) es concreto y demostrable en 15 minutos |
| 3 | No depende de datos sensibles | ✅ | Usa código de prueba con bugs plantados; no requiere datos de producción ni repos privados |
| 4 | No requiere infra costosa | ✅ | GitHub Actions free tier + Ollama local o API de bajo costo (~$0.001/PR). Sin costos fijos. |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Sección "Reflexión AI" completa con plantilla estructurada para documentar cómo la IA asistió durante el lab |
| 6 | No asume nivel homogéneo | ✅ | Sección de extensión rica (meta-evaluador, dashboard, custom rules) permite que equipos más avanzados vayan más lejos |

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

**Score general:** 23/24 (diseño 8/9 + anti-criterios 6/6 + estructura 9/9)

### Fortalezas
- Demo en 15 minutos con bugs plantados específicos es una de las mejores definiciones de éxito del programa — concreta y verificable
- El "Terreno a explorar" es excepcionalmente bueno: preguntas sobre alucinaciones de línea, chunking de context window y calidad de comentarios van directo al núcleo del patrón
- La extensión (meta-evaluador LLM + feedback loop via emoji reactions) escala naturalmente sin complejizar el MVP
- Structured outputs como skill central está bien justificado con un caso de uso que lo requiere genuinamente
- La plantilla de Reflexión AI incluye el eje "¿La IA cambió cómo trabajó el equipo?" — la pregunta más valiosa del programa

### Gaps / Mejoras sugeridas
- AWS queda subrepresentado: mencionar CodeGuru Reviewer como comparador conceptual (no obligatorio) agregaría contexto sobre el estado del arte y una referencia AWS natural
- El MVP no especifica qué hacer cuando el JSON del LLM es inválido — agregar una línea sobre estrategia de retry/fallback haría el alcance más robusto para equipos menos experimentados
