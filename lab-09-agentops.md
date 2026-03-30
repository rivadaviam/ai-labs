# Lab 09: AgentOps — Agente Autónomo de Diagnóstico de Infraestructura

**Duración del equipo:** 2–3 personas
**MVP:** 1 semana | **Extensión:** 1 mes
**Nivel:** Intermedio–Avanzado

---

## 1. Nombre del lab

**AgentOps** — Agente de IA con herramientas para diagnosticar problemas de infraestructura desde lenguaje natural

---

## 2. Skill principal

**AI Agents + Tool Use** — Diseño de agentes con ciclo ReAct (Reason + Act), function calling, manejo de estado, y límites de seguridad para operaciones autónomas.

---

## 3. Problema a resolver

Cuando hay un incidente de producción a las 2am, el engineer de guardia necesita ejecutar 10-15 comandos en distintos sistemas (logs, métricas, health checks, queries de DB) antes de entender qué está pasando. Este proceso es lento, propenso a errores bajo presión, y el conocimiento sobre qué comandos ejecutar está disperso en runbooks desactualizados. No existe una forma de decir "el endpoint `/api/users` responde en 8 segundos" y que el sistema razone qué revisar y en qué orden.

---

## 4. Producto objetivo

**Una CLI interactiva** que acepta una descripción del problema en lenguaje natural, ejecuta autónomamente una secuencia de diagnósticos usando herramientas, y produce un reporte de causa raíz probable con evidencia.

**Demo en 15 minutos:** Ejecutar `agentops diagnose "el endpoint /api/users responde en 8 segundos en producción"`. En el terminal, el agente razona en voz alta (visible al usuario), llama tools en secuencia (check logs → check DB query times → check memory usage → check active connections), y produce un reporte final: `"Causa probable: N+1 query en UserRepository. Evidencia: 847 queries en 3 segundos durante las últimas llamadas al endpoint. Recomendación: agregar eager loading en la relación User → Orders."` El demo incluye mostrar el trace completo de razonamiento del agente.

---

## 5. Alcance del MVP (1 semana)

- CLI con comando `diagnose` que acepta descripción del problema en lenguaje natural
- Set de 6–8 tools implementadas con datos simulados realistas: `get_recent_logs(service, minutes)`, `get_db_query_times(endpoint)`, `get_memory_usage(service)`, `get_active_connections()`, `get_recent_deploys(hours)`, `run_health_check(service)`
- Loop ReAct implementado manualmente o con LangChain/LangGraph: el agente razona, elige una tool, ejecuta, observa el resultado, razona de nuevo
- Razonamiento visible en terminal en tiempo real (streaming del "thought" del agente antes de cada tool call)
- Límite duro de pasos: máximo 10 tool calls por diagnóstico para evitar loops infinitos
- Reporte final estructurado: causa probable, evidencia (qué tool/resultado lo sustenta), nivel de confianza, próximos pasos recomendados

---

## 6. Extensión (1 mes)

- Conectar tools reales: queries a PostgreSQL, llamadas a Prometheus/Grafana API, parseo de logs de archivos o CloudWatch
- Modo "runbook automático": si el agente identifica el problema con confianza > 0.85, puede proponer y (con confirmación explícita del usuario) ejecutar la remediación
- Historial de diagnósticos en SQLite: los diagnósticos anteriores informan el razonamiento ("este tipo de problema fue resuelto 3 veces antes así")
- Interface web con visualización del grafo de razonamiento (nodos = tool calls, edges = reasoning steps)
- Integración con PagerDuty o OpsGenie para iniciar un diagnóstico automáticamente desde un alert

---

## 7. Stack sugerido

**Gratis / open-source:**
- Ollama + Llama 3.1 8B (soporta function calling nativo desde llama3.1)
- Implementación manual del loop ReAct en Python (recomendado para aprender el mecanismo antes de usar frameworks)
- Rich (Python) para output de terminal con colores, tablas, y spinners
- SQLite para historial de diagnósticos
- Typer para CLI

**Alternativas cloud:**
- OpenAI GPT-4o mini (excelente tool calling, muy económico para agentes iterativos)
- Anthropic Claude 3.5 Haiku (tool use muy confiable)
- Groq Llama3.1 70B (gratis, latencia muy baja para loops de agente)

---

## 8. Criterios de evaluación

| Criterio | Métrica mínima de éxito |
|---|---|
| Precisión diagnóstica | 4 de 5 escenarios de prueba del lab producen un reporte con la causa probable correcta |
| Eficiencia | Promedio de < 7 tool calls por diagnóstico exitoso (sin loops redundantes) |
| Razonamiento transparente | El equipo puede leer el trace y explicar por qué el agente tomó cada decisión |
| Control de loops | El agente nunca excede el límite de pasos en ningún escenario de prueba, incluyendo inputs ambiguos |
| Calidad del reporte | Evaluación ciega de 3 engineers: reporte comprensible y accionable en ≥ 80% de los casos |

---

## 9. Riesgos y desafíos

- **El agente entra en loops** — Llama la misma tool con los mismos parámetros repetidamente. Solución: incluir el historial completo de tool calls en el contexto y penalizar explícitamente la repetición en el system prompt.
- **El agente alucina resultados de tools** — En lugar de llamar la tool, el LLM "inventa" el resultado. Solución: system prompt muy explícito de que DEBE usar tools para obtener datos, nunca asumir o inventar valores.
- **Function calling poco confiable en modelos pequeños** — Algunos modelos no generan JSON válido para tool calls consistentemente. Solución: usar modelos con soporte probado (llama3.1, mistral-nemo) o agregar validación de schema + retry automático.
- **Scope creep hacia tools reales** — Los equipos quieren conectar herramientas reales desde el día 1 y se pierden en problemas de infraestructura. Las tools con datos simulados pero realistas son suficientes para el MVP y permiten enfocarse en el agente.

---

## 10. Reflexión AI (completar al terminar el lab)

Cada equipo completa este template y lo comparte con el programa. Alimenta el loop de mejora entre ciclos.

```
## Reflexión AI — AgentOps — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| | | |

### Mayor impacto de IA en este lab
[ej: diseñar el system prompt del agente, generar datos simulados realistas para las tools, debuggear loops del agente]

### Momento donde la IA no ayudó o introdujo problemas
[ej: el agente sugirió una arquitectura de tools demasiado compleja para el MVP, o alucinó resultados de tools]

### ¿La IA cambió cómo trabajó el equipo (no solo lo aceleró)?
[ ] Sí — describir: ___
[ ] No, solo aceleró tareas existentes

### Recomendación para el próximo equipo
[ej: "Empezá con el loop ReAct manual antes de usar frameworks — la IA te puede ayudar a entender el mecanismo paso a paso"]
```
