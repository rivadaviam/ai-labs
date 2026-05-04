# Scorecard — Lab 06: Resilient LLM Gateway

**Skill principal:** Resiliencia de sistemas AI en producción
**Patrón de IA:** Cross-region inference, retry con backoff/jitter, circuit breaker para LLMs
**Fecha de análisis:** 2026-03-31

---

## Criterios de Diseño

| # | Criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | Producto objetivo claro | ✅ | Servicio FastAPI `llm-gateway` con endpoints `/invoke` y `/metrics`, demo de 15 minutos con pasos exactos y outputs esperados |
| 2 | Alcanzable en 24–36 horas | ✅ | MVP estructurado en 2 semanas: semana 1 gateway base con retry, semana 2 circuit breaker + métricas + demo |
| 3 | Anclado en patrón de IA real | ✅ | Cross-region inference profiles de Bedrock es un patrón de producción vigente; retry/circuit breaker son patrones de resiliencia estándar para APIs LLM |
| 4 | Problema realista | ✅ | ThrottlingException en eventos de alta demanda (Black Friday) es un problema concreto y frecuente en equipos que usan Bedrock en producción |
| 5 | Requiere decisiones técnicas | ✅ | El equipo debe diseñar umbrales del circuit breaker, ventana deslizante, parámetros de jitter, y decidir qué métricas exponer — no hay respuesta única |
| 6 | Comparable entre equipos | ✅ | Demo estandarizada con `load_test.py` y payload de `/metrics` definido; equipos pueden comparar failure_rate, latency_p95, region_distribution |
| 7 | Stack al servicio del concepto | ✅ | FastAPI/boto3/asyncio son herramientas para construir el gateway; el concepto central es resiliencia, no aprender el stack |
| 8 | AWS preferido pero no obligatorio | ✅ | Alternativa local con `mock_bedrock.py` explícitamente documentada y recomendada para las primeras 1.5 semanas; costo $0 posible |
| 9 | Costo AWS mínimo | ✅ | "< $3 para las pruebas de carga (50-100 requests × 3-5 runs)"; mock local permite desarrollo completo sin costo |

**Diseño: 9/9 ✅ · 0/9 ⚠️ · 0/9 ❌**

---

## Anti-criterios

| # | Anti-criterio | Estado | Evidencia |
|---|---|---|---|
| 1 | No basado en certificación | ✅ | El objetivo es construir un gateway funcional y demostrar resiliencia con datos, no responder preguntas teóricas |
| 2 | No es walkthrough sin producto | ✅ | Hay un producto concreto (el gateway) con demo medible; no es un tutorial paso a paso sin decisiones propias |
| 3 | No depende de datos sensibles | ✅ | Usa datos sintéticos (descripciones de producto ficticias) y mock local; sin datos de producción requeridos |
| 4 | No requiere infra costosa | ✅ | MVP completo posible con mock local ($0); AWS opcional y estimado en < $3 |
| 5 | Incluye eje AI-asiste-al-equipo | ✅ | Sección "Reflexión AI" con templates específicos (circuit breaker, mock de Bedrock, jitter) y ejemplos de donde la IA puede fallar |
| 6 | No asume nivel homogéneo | ✅ | Extensiones graduales (tres regiones, Prometheus, caché semántico, ECS Fargate); mock local reduce la barrera de entrada |

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

**Score general:** 24/24 (diseño 9/9 + anti-criterios 6/6 + estructura 9/9)

### Fortalezas
- Demo de 15 minutos con outputs esperados exactos hace que el criterio de éxito sea completamente inequívoco
- Mock de Bedrock con throttling configurable resuelve elegantemente el problema de costo y dependencia de AWS durante el desarrollo
- Las preguntas de "Terreno a explorar" son precisas y generativas: tradeoffs reales (latencia vs. resiliencia, estado en memoria vs. distribuido)
- La sección de Reflexión AI incluye ejemplos concretos de donde la IA falla (circuit breaker sin jitter, asyncio.sleep incorrecto), no solo donde ayuda
- El payload de `/metrics` definido permite comparación directa entre equipos sin ambigüedad

### Gaps / Mejoras sugeridas
- El script `load_test.py` se menciona como entregable pero no hay indicación de si se provee como starter o el equipo lo construye desde cero — aclararlo evita confusión
- La opción `--force-throttle` del load test implica que puede simular throttling artificialmente, pero no queda claro si eso requiere modificar el mock o el gateway — valdría una línea de explicación
- La extensión de caché semántico introduce un concepto (embeddings/similarity) que es significativamente más complejo que el resto — podría marcarse con un nivel de dificultad diferenciado
