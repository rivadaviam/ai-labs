# Scorecard — Lab 03: AI Governance Scanner

**Skill principal:** AI Governance y seguridad de infraestructura cloud
**Patrón de IA:** AI-asiste-al-equipo (generación de casos de prueba, políticas correctivas, templates)
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | CLI `ai-governance-scan` con output HTML + JSON, demo de 15 minutos con pasos numerados y resultados concretos |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP en dos fases bien delimitadas: scanner core + reporte HTML. Stack mínimo (boto3, Typer, Jinja2) |
| 3 | Anclado en patrón de IA real | ✅ | Enforcement de AI governance via IAM condition keys (`bedrock:GuardrailIdentifier`) — patrón vigente y real en producción |
| 4 | Problema realista | ✅ | "Microservicio que olvida el guardrail requerido — nadie lo detecta hasta un audit manual o un incidente" — escenario creíble |
| 5 | Requiere decisiones técnicas | ✅ | El equipo debe decidir cómo manejar wildcards, paginación silenciosa, inline vs. managed policies, y mínimo privilegio en remediación |
| 6 | Comparable entre equipos | ✅ | Output JSON + HTML estandarizado, misma métrica (% roles COMPLIANT), demo reproducible con roles de prueba definidos |
| 7 | Stack al servicio del concepto | ✅ | Solo boto3 + Typer + Jinja2 — tecnología mínima que no distrae del concepto de governance |
| 8 | AWS preferido pero no obligatorio | ✅ | Sección explícita "Alternativa sin AWS": scanner de repos GitHub para equipos sin acceso a IAM completo |
| 9 | Costo AWS mínimo | ✅ | "Costo estimado: $0 — todas las operaciones son llamadas a IAM API sin costo" |

**Diseño: 9/9 ✅ · 0/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | El objetivo es construir una herramienta funcional, no responder preguntas de examen |
| 2 | No es walkthrough sin producto | ✅ | Se construye una CLI con output real y demo verificable de remediación aplicada |
| 3 | No depende de datos sensibles | ✅ | Usa roles IAM de prueba creados por script; alternativa apunta a repos locales o GitHub API pública |
| 4 | No requiere infra costosa | ✅ | $0 estimado; toda la lógica corre localmente contra IAM API sin costo |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Sección Reflexión AI con tabla de herramientas, prompts, edge cases y recomendación al próximo equipo |
| 6 | No asume nivel homogéneo | ✅ | Nivel declarado "Intermedio–Avanzado", sección Extensión para equipos más avanzados, alternativa sin AWS para equipos sin acceso |

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

**Score general: 24/24 (diseño 9/9 + anti-criterios 6/6 + estructura 9/9)**

### Fortalezas
- Demo de 15 minutos totalmente scripted con resultados verificables (scan → fix → re-scan) — hace que el producto sea indiscutible
- El problema técnico central (IAM condition keys para Bedrock guardrails) es específico, real y no obvio — fuerza aprendizaje genuino
- Alternativa sin AWS bien diseñada: misma estructura, mismo output, diferente target — no es un fallback de segundo nivel
- La sección "Terreno a explorar" contiene preguntas de diseño no-triviales que el equipo debe resolver (wildcards, paginación silenciosa, mínimo privilegio en remediación)
- Reflexión AI incluye template con preguntas sobre momentos donde la IA falló — no solo donde ayudó

### Gaps / Mejoras sugeridas
- El lab no menciona cómo crear el Bedrock Guardrail de prueba requerido para el ARN de referencia — puede ser un bloqueante si el equipo no tiene uno previo
- La fase 2 del MVP (reporte HTML) podría exceder el tiempo disponible si el equipo se atasca en el parser de políticas IAM — podría marcarse como opcional para equipos más lentos
- No hay criterios explícitos de comparación entre equipos más allá del formato de output — agregar 2-3 métricas concretas (ej. tiempo de scan para N roles, tasa de falsos positivos en wildcards) facilitaría el show & tell
