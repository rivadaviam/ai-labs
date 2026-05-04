# Scorecard — Lab 02: Guardrails Calibration Tool

**Skill principal:** Evaluación y calibración de sistemas de moderación AI
**Patrón de IA:** Guardrails / Content moderation measurement & iterative calibration
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | CLI `guardrail-bench` con reporte HTML, demo de 15 minutos detallada paso a paso |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP dividido en dos fases concretas; 40 test cases + CLI + HTML report es realista para 2–3 personas en 3 semanas |
| 3 | Anclado en patrón de IA real | ✅ | Mide falsos positivos/negativos en guardrails de producción — patrón activo en equipos que despliegan LLMs |
| 4 | Problema realista | ✅ | "El equipo de soporte reporta quejas pero nadie sabe si es 2% o 30% de queries legítimas" — escenario creíble |
| 5 | Requiere decisiones técnicas | ✅ | Diseño del dataset, qué políticas cubrir, cuándo ajustar guardrail vs. dataset, cómo presentar métricas a stakeholders |
| 6 | Comparable entre equipos | ✅ | Score de accuracy global + tasa de FP/FN por política permiten comparar resultados entre equipos sobre el mismo dataset |
| 7 | Stack al servicio del concepto | ✅ | Python + Typer + Jinja2 + Chart.js son herramientas estándar; Bedrock Guardrails es el objeto de estudio, no el aprendizaje del stack |
| 8 | AWS preferido pero no obligatorio | ✅ | Alternativa explícita: "Reemplazar Bedrock Guardrails por OpenAI Moderation API (gratuita)" |
| 9 | Costo AWS mínimo | ✅ | "< $1 por equipo — 40 casos × 5 ejecuciones = ~$0.30" — muy por debajo del umbral de $5–10 |

**Diseño: 9/9 ✅ · 0/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | Objetivo es construir y medir, no estudiar para un examen |
| 2 | No es walkthrough sin producto | ✅ | El producto (`guardrail-bench` + reporte HTML) es el centro del lab, no un tutorial guiado |
| 3 | No depende de datos sensibles | ✅ | El dataset es sintético (`financial_qa.jsonl` creado por el equipo); no se requieren datos de producción |
| 4 | No requiere infra costosa | ✅ | Sin servidores, sin bases de datos; solo llamadas API con costo estimado < $1 |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Reflexión AI detalla uso de Copilot, Claude, Codex para generar test cases, runner y template HTML |
| 6 | No asume nivel homogéneo | ✅ | Extensiones opcionales (adversarial generation, modo interactive, comparación multi-guardrail) permiten equipos más avanzados ir más lejos |

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
- Demo de 15 minutos completamente guionizada con minutos específicos — hace el producto objetivo tangible y verificable
- Costo AWS extraordinariamente bajo (~$0.30 por equipo), con alternativa gratuita bien especificada
- El ciclo de calibración v1→v2 (FP bajó de 23% a 4%) convierte el lab en un proceso repetible, no un ejercicio único
- Las preguntas de "Terreno a explorar" son genuinamente difíciles (bias en selección de dataset, guardrail en producción vs. lab) — fuerzan reflexión más allá del código
- La Reflexión AI tiene prompts específicos que guían la introspección del equipo sobre dónde la IA ayudó y dónde falló

### Gaps / Mejoras sugeridas
- No menciona cómo manejar rate limits de `ApplyGuardrail` al correr 40+ casos seguidos — podría ser un bloqueante no obvio para equipos sin experiencia con Bedrock
- El dataset de 40 casos es sintético y financiero por default; un párrafo sobre cómo adaptar el dominio a otros contextos (ecommerce, salud, legal) aumentaría la reutilización del lab
- La comparación entre equipos podría ser más explícita: proponer que todos usen el mismo `financial_qa.jsonl` base para que los resultados sean directamente comparables en el show & tell
