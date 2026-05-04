# Scorecard — Lab 08: PromptBench — Evaluación Sistemática de Prompts de Producción

**Skill principal:** Prompt Evaluation + LLM-as-Judge
**Patrón de IA:** Construcción de pipelines de evaluación con LLM-as-judge y métricas automáticas
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | "Una CLI + reporte HTML que, dado un dataset de pares (input, expected_output) y dos versiones de un prompt, ejecuta evaluación automática y genera un reporte comparativo" — demo de 15 minutos bien especificado |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP es CLI + runner + 3 modos de evaluación + reporte HTML; stack sugerido (Typer, asyncio, Jinja2) es directo y bien acotado |
| 3 | Anclado en patrón de IA real | ✅ | LLM-as-judge es un patrón vigente y de alta relevancia en producción; el lab incluye exact match, embeddings cosine y LLM-judge como modos concretos |
| 4 | Problema realista | ✅ | "Cada cambio se valida 'a ojo'… Los cambios de prompt van a producción sin red de seguridad" — escenario reconocible y frecuente en equipos que usan IA en producción |
| 5 | Requiere decisiones técnicas | ✅ | Elección de modo de evaluación, calibración del judge, manejo de concurrencia, diseño del dataset, interpretación de contradicciones entre métricas — todas son decisiones no triviales |
| 6 | Comparable entre equipos | ✅ | Todos ejecutan `promptbench compare` sobre datasets JSONL con el mismo formato; scores, regresiones y reportes HTML permiten comparación directa |
| 7 | Stack al servicio del concepto | ✅ | El stack (Typer, asyncio, Jinja2, sentence-transformers) es instrumental; el foco está en los conceptos de evaluación, no en dominar las herramientas |
| 8 | AWS preferido pero no obligatorio | ⚠️ | No se menciona AWS en ningún punto; el stack es todo local u OpenAI/Anthropic. La ausencia de AWS no es un problema de diseño, pero el criterio no se satisface explícitamente |
| 9 | Costo AWS mínimo | ⚠️ | Sin componentes AWS. El costo de API (OpenAI GPT-4o mini ~$0.002/100 ejemplos, Claude Haiku) es mínimo; el criterio aplica de forma lateral |

**Diseño: 7/9 ✅ · 2/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | El objetivo es construir un framework de evaluación funcional, no estudiar conceptos para un examen |
| 2 | No es walkthrough sin producto | ✅ | Hay un producto concreto: CLI + reporte HTML con demo ejecutable en 15 minutos |
| 3 | No depende de datos sensibles | ✅ | El dataset de ejemplo usa tickets de soporte genéricos en formato JSONL; el comando `dataset expand` permite generar casos sintéticos |
| 4 | No requiere infra costosa | ✅ | Stack principal es 100% local (Ollama + sentence-transformers); alternativas cloud tienen costos de centavos por corrida |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Sección "Reflexión AI" incluye preguntas sobre qué usó IA, cuándo no ayudó, si cambió el modo de trabajo del equipo — bien integrado |
| 6 | No asume nivel homogéneo | ✅ | MVP es claro para nivel intermedio; extensiones (CI/CD, A/B en producción, multi-turn) dan espacio a equipos más avanzados |

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

**Score general:** 22/24 (diseño 7/9 + anti-criterios 6/6 + estructura 9/9)

### Fortalezas
- Problema de producción auténtico y altamente reconocible — la validación "a ojo" de prompts es un dolor real en equipos con IA
- Producto objetivo muy bien definido: CLI ejecutable + reporte HTML con demo de 15 minutos acotado y verificable
- LLM-as-judge es un patrón de alta relevancia actual; el lab enseña tanto la implementación como sus limitaciones (sesgos, calibración, contradicciones entre métricas)
- Stack completamente local como opción primaria (Ollama + sentence-transformers) elimina dependencia de APIs y costo
- Terreno a explorar cubre preguntas de diseño no triviales: representatividad del dataset, reproducibilidad, decisiones para PM
- Estructura completa, sin secciones faltantes, con reflexión AI bien diseñada

### Gaps / Mejoras sugeridas
- AWS está completamente ausente — si el programa tiene preferencia por AWS, podría sugerirse S3 para almacenar datasets/reportes o CloudWatch para logging de evaluaciones en producción
- El criterio de "comparable entre equipos" pasa, pero el formato de reporte HTML no especifica un esquema fijo de métricas; dos equipos podrían generar reportes con métricas distintas según el modo elegido — sería útil definir un schema mínimo obligatorio para facilitar la comparación en el show & tell
- La sección "Reflexión AI" está marcada como opcional ("no es un entregable obligatorio") — para reforzar el eje de AI-asiste-al-equipo podría hacerse semi-obligatoria al menos en el show & tell
