# Scorecard — Lab 01: RAG Health Monitor

**Skill principal:** Observabilidad de sistemas AI
**Patrón de IA:** Retrieval-Augmented Generation (RAG) — monitoreo y detección de degradación silenciosa
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | Sistema de monitoreo con Lambda + EventBridge + CloudWatch metrics + dashboard + alarms, con demo de 15 minutos completamente especificada |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP en dos fases bien delimitadas; costo estimado < $2; stack acotado y alternativa open-source disponible |
| 3 | Anclado en patrón de IA real | ✅ | RAG observability es patrón vigente y crítico; el bug de embedding mismatch es un problema real documentado en producción |
| 4 | Problema realista | ✅ | "CloudWatch no registra errores, el pipeline técnicamente funciona, los usuarios reciben respuestas vacías" — escenario creíble y frecuente en equipos con RAG en producción |
| 5 | Requiere decisiones técnicas | ✅ | Calibración de umbrales de alarma, selección de canary queries representativas, diseño del runbook, elección de métricas — "Terreno a explorar" fuerza estas decisiones |
| 6 | Comparable entre equipos | ✅ | Las 4 métricas custom estandarizadas (`EmbeddingModelConsistency`, `RetrievalQualityScore`, `CanaryLatencyMs`, `DocumentsRetrieved`) y el script de "break" permiten comparación directa |
| 7 | Stack al servicio del concepto | ✅ | AWS prescripto porque los servicios (Bedrock, OpenSearch Serverless, CloudWatch) son el objeto de aprendizaje; alternativa open-source ofrecida para quienes no usan AWS |
| 8 | AWS preferido pero no obligatorio | ✅ | Stack AWS completo recomendado + alternativa open-source equivalente (Ollama, Chroma, Prometheus/Grafana) explícitamente documentada |
| 9 | Costo AWS mínimo | ✅ | "Costo estimado: < $2 por equipo para todo el lab" — dentro del rango objetivo |

**Diseño: 9/9 ✅ · 0/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | El objetivo es construir un sistema funcional de monitoreo, no responder preguntas de examen |
| 2 | No es walkthrough sin producto | ✅ | El producto (sistema de monitoreo desplegado y funcionando con demo de 15 min) está claramente definido como entregable |
| 3 | No depende de datos sensibles | ✅ | Corpus de documentos de prueba (mínimo 50 documentos), canary queries propias del equipo — sin dependencia de datos de producción |
| 4 | No requiere infra costosa | ✅ | < $2 por equipo; OpenSearch Serverless y Lambda son pay-per-use; alternativa local disponible con costo $0 |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Sección "Reflexión AI" incluye template con herramientas usadas, impacto, momentos donde la IA no ayudó, y recomendación para el próximo equipo |
| 6 | No asume nivel homogéneo | ✅ | Extensiones opcionales para equipos avanzados; alternativa open-source para quienes no tienen acceso a AWS; runbook como entregable permite distintos niveles de profundidad |

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
- Producto excepcionalmente bien especificado: las 4 métricas custom, el script de "break", y el guión de demo de 15 minutos eliminan ambigüedad sin quitar autonomía técnica al equipo
- El problema ancla (embedding model mismatch) es concreto, reproducible y técnicamente auténtico — no un escenario fabricado para el lab
- La sección "Terreno a explorar" es de alta calidad: las preguntas fuerzan decisiones de diseño reales (calibración de umbrales, representatividad de canary queries, degradación gradual vs. abrupta)
- Reflexión AI bien estructurada con template que captura tanto el impacto positivo como los momentos de falla — útil para aprendizaje organizacional
- Alternativa open-source completamente especificada, servicio por servicio

### Gaps / Mejoras sugeridas
- El corpus de "mínimo 50 documentos" no especifica dominio ni cómo generarlo — un equipo sin documentos disponibles puede perder tiempo en esto antes de llegar a la lógica del lab
- No hay guía sobre cómo validar que el entorno está listo antes de empezar (health check previo al lab) — OpenSearch Serverless puede tener latencia de provisionamiento que sorprende
- La métrica `RetrievalQualityScore` (similarity promedio) asume que el equipo sabe cómo calcularla; sería útil una nota sobre el método esperado (cosine similarity, dot product) o dejarlo explícitamente como decisión del equipo
