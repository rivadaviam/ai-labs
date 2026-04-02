# Lab 06: Resilient LLM Gateway — Anti-Throttling con Cross-Region Inference

**Equipo:** 2–3 personas
**Nivel:** Intermedio

---

## 1. Nombre del lab

**Resilient LLM Gateway** — Proxy con observabilidad que implementa anti-throttling con cross-region inference, retry inteligente, y circuit breaker, demostrando con datos que funciona bajo carga

---

## 2. Skill principal

**Resiliencia de sistemas AI en producción** — Diseño de patrones de resiliencia (retry, circuit breaker, fallback), observabilidad con métricas en tiempo real, y validación bajo condiciones de carga controladas.

---

## 3. Problema a resolver

Una aplicación de ecommerce llama a Bedrock directamente para generar descripciones de producto y responder preguntas de clientes. Durante eventos de alta demanda (Black Friday, lanzamientos), Bedrock retorna `ThrottlingException` en un porcentaje significativo de los requests. Las opciones inmediatas son: Provisioned Throughput (costoso y requiere compromiso de tiempo), ignorar el problema (inaceptable en producción), o implementar una capa de resiliencia.

El equipo debe construir esa capa y demostrar con datos que funciona bajo carga — no solo teorizar que funciona.

---

## 4. Producto objetivo

Un servicio FastAPI `llm-gateway` que actúa como proxy entre la aplicación y Bedrock, implementando:

1. **Cross-region inference routing:** usa Bedrock cross-region inference profiles (`us.anthropic.claude-3-5-haiku-20251001-v1:0`) para distribuir automáticamente entre `us-east-1` y `us-west-2`
2. **Retry con exponential backoff + jitter:** en `ThrottlingException`, reintenta con delay exponencial + jitter aleatorio para evitar thundering herd
3. **Circuit breaker:** si más del N% de requests en una ventana deslizante falla, el circuit breaker se abre y los requests van directo al fallback sin intentar el primario por X segundos
4. **Endpoint `/metrics`:** retorna JSON con el estado del gateway en tiempo real

Payload de `/metrics`:
```json
{
  "total_requests": 50,
  "throttled_count": 17,
  "retry_success_count": 15,
  "circuit_breaker_open": false,
  "circuit_breaker_trips": 1,
  "region_distribution": { "us-east-1": 33, "us-west-2": 17 },
  "latency_p50_ms": 312,
  "latency_p95_ms": 847,
  "failure_rate_percent": 0
}
```

**Validaciones sugeridas:**

- Correr una prueba de carga directa contra Bedrock (sin gateway) y registrar la tasa de throttling
- Correr la misma prueba a través del gateway y verificar que la tasa de éxito mejora significativamente
- Verificar que `/metrics` refleja correctamente la distribución de requests entre regiones, retries exitosos, y estado del circuit breaker
- Forzar throttling sostenido y verificar que el circuit breaker se abre y rutea al fallback
- Verificar que tras el período de timeout, el circuit breaker pasa a half-open y prueba la región primaria

*En el show & tell, el equipo muestra las validaciones que corrió y centra la conversación en decisiones tomadas, problemas encontrados, y lecciones aprendidas.*

---

## 5. Alcance del MVP

**Primera fase — Gateway base con retry y cross-region:**
- FastAPI con endpoint `/invoke`
- Retry con exponential backoff + jitter
- Cross-region fallback funcional
- Mock de Bedrock para desarrollo local sin costo

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas.

**Segunda fase — Circuit breaker + métricas + load test:**
- Circuit breaker con ventana deslizante configurable
- Endpoint `/metrics`
- Script `load_test.py`
- Validaciones completas funcionando

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo.

**Fuera del MVP:** autenticación del gateway, rate limiting hacia clientes upstream, persistencia de métricas, deploy en ECS.

---

## 6. Extensión

- **Tres regiones:** Agregar `eu-west-1` como tercer fallback. Circuit breaker independiente por región. Medir el impacto en latencia con la región europea.
- **Métricas en Prometheus + Grafana:** Reemplazar el endpoint JSON con métricas en formato Prometheus. Dashboard en tiempo real durante la prueba de carga.
- **Caché semántico:** Para queries muy similares, retornar la respuesta cacheada. Medir hit rate y su impacto en latencia y costo.
- **Rate limiter por cliente:** Si múltiples servicios usan el gateway, limitar por `client_id` en el header.
- **Deploy en ECS Fargate:** Containerizar con Docker y desplegar con ALB adelante. Comparar latencia local vs. AWS.

---

## 7. Stack sugerido

**AWS (recomendado):**

| Servicio | Rol |
|----------|-----|
| Bedrock + Cross-region profiles | `us.anthropic.claude-3-5-haiku-20251001-v1:0` — routing multi-región automático |
| Python 3.12 + FastAPI + boto3 | Framework del proxy |
| asyncio | Concurrencia de requests |
| prometheus_client | Métricas en formato estándar |

Costo estimado: < $3 para las pruebas de carga (50-100 requests × 3-5 runs).

**Alternativa local (costo $0):**
Un servidor mock `mock_bedrock.py` que simula la API de Bedrock con throttling configurable:
```
python mock_bedrock.py --throttle-rate 0.4 --port 9090
BEDROCK_ENDPOINT=http://localhost:9090 uvicorn gateway:app --port 8080
```
Permite desarrollar y demostrar el gateway completo — incluido el circuit breaker — sin incurrir en ningún costo de AWS. Recomendado para las primeras 1.5 semanas de desarrollo.

---

## 8. Terreno a explorar

- ¿Cómo funciona un circuit breaker? ¿Cuáles son los estados (closed, open, half-open) y las transiciones entre ellos?
- ¿Qué es throttling en el contexto de APIs de LLM y por qué cross-region inference ayuda a distribuirlo?
- ¿Qué es thundering herd y cómo el jitter en el retry lo mitiga? ¿Cuánto jitter es suficiente?
- ¿Cuál es el tradeoff entre resiliencia y latencia en un gateway? ¿Cuánta latencia extra es aceptable para ganar resiliencia en producción?
- ¿Qué limitaciones tiene guardar el estado del circuit breaker en memoria del proceso? ¿Qué implicancias tiene si el gateway se reinicia?
- ¿Por qué es importante que los parámetros del circuit breaker (umbral, ventana, timeout) sean configurables sin deployar?
- ¿Cuándo las métricas del mock son útiles para el desarrollo y cuándo no son suficientes para validar el comportamiento real?
- ¿La IA implementó correctamente el circuit breaker con todos sus estados (closed, open, half-open), o simplificó la lógica de forma que no era útil en la práctica?
- ¿En qué parte del lab la IA ahorró más tiempo: el mock de Bedrock, la lógica de resiliencia, o el load test?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — Resilient LLM Gateway — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Codex / Copilot | Generar el skeleton del gateway FastAPI y el cliente boto3 async | |
| Claude | Diseñar e implementar el circuit breaker y revisar la lógica de jitter | |
| Windsurf | Refactorizar el código en módulos y generar el load_test.py | |

### Mayor impacto de IA en este lab
[¿La IA implementó correctamente el circuit breaker con ventana deslizante? ¿Ayudó a
entender la diferencia entre cross-region inference profiles y multi-region endpoints?
¿Generó el mock de Bedrock que simulaba throttling correctamente?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA generó una implementación de circuit breaker sin jitter que causaba thundering
herd? ¿Usó asyncio.sleep incorrectamente dentro de FastAPI? ¿El código de retry no
re-levantaba la excepción correctamente después de agotar los reintentos?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Implementar el mock de Bedrock primero y desarrollar todo el gateway contra el
mock — acelera el ciclo y evita costos. El circuit breaker es el componente más difícil
y la IA tiende a simplificarlo incorrectamente: pedirle que explique el algoritmo
antes de que escriba el código."]
```
