# Lab 09: AgentOps — Agente Autónomo de Diagnóstico de Infraestructura

**Equipo:** 2–3 personas
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

**Validaciones sugeridas:**

- Ejecutar un diagnóstico con un problema conocido y verificar que el agente llega a la causa raíz correcta
- Verificar que el trace de razonamiento es legible y muestra claramente por qué el agente eligió cada tool
- Probar con un problema ambiguo y verificar que el agente no alucina resultados de tools ni entra en un loop
- Verificar que el límite de pasos (máximo 10 tool calls) funciona correctamente
- Probar con al menos 2 escenarios de diagnóstico distintos para verificar generalidad

*En el show & tell, el equipo muestra las validaciones que corrió y centra la conversación en decisiones tomadas, problemas encontrados, y lecciones aprendidas.*

---

## 5. Alcance del MVP

**Primera fase — Tools y loop ReAct básico:**
- CLI con comando `diagnose` que acepta descripción del problema en lenguaje natural
- Set de 6–8 tools implementadas con datos simulados realistas: `get_recent_logs(service, minutes)`, `get_db_query_times(endpoint)`, `get_memory_usage(service)`, `get_active_connections()`, `get_recent_deploys(hours)`, `run_health_check(service)`
- Loop ReAct implementado manualmente o con LangChain/LangGraph: el agente razona, elige una tool, ejecuta, observa el resultado, razona de nuevo

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas.

**Segunda fase — Observabilidad y robustez:**
- Razonamiento visible en terminal en tiempo real (streaming del "thought" del agente antes de cada tool call)
- Límite duro de pasos: máximo 10 tool calls por diagnóstico para evitar loops infinitos
- Reporte final estructurado: causa probable, evidencia (qué tool/resultado lo sustenta), nivel de confianza, próximos pasos recomendados

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo.

---

## 6. Extensión

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

## 8. Terreno a explorar

- ¿Cómo se manifiesta un loop en un agente ReAct? ¿Qué patrones en el trace indican que el agente está repitiendo pasos sin avanzar?
- ¿Qué hace que un LLM alucine resultados de tools en lugar de llamarlas? ¿Cómo el system prompt puede influir en este comportamiento?
- ¿Cómo se mide la eficiencia de un agente? ¿Cuándo más tool calls indica confusión vs. complejidad legítima del problema?
- ¿Qué información de una tool es necesaria en el contexto del agente vs. qué información es ruido que afecta las decisiones?
- ¿Cómo varía el comportamiento del agente entre modelos? ¿Qué capacidades son críticas para function calling confiable?
- ¿Qué hace que el razonamiento de un agente sea legible en el trace? ¿Cuándo es opaco y por qué?
- ¿Cuándo las tools con datos simulados son suficientes para validar el agente y cuándo no lo son?
- ¿La IA asistente ayudó a diseñar el system prompt del agente, o el equipo tuvo que iterarlo manualmente hasta que el comportamiento fuera útil?
- ¿La IA que usaste para construir el agente entró en los mismos problemas (loops, alucinaciones de tools) que el agente que estás construyendo?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — AgentOps — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Codex / Copilot | Implementar las tools simuladas y el loop ReAct | |
| Claude / ChatGPT | Diseñar el system prompt del agente y generar datos simulados | |
| Windsurf / Cursor | Debuggear el comportamiento del agente y refactorizar | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en diseñar el system prompt del agente, en generar datos simulados
realistas para las tools, en implementar el loop ReAct, o en debuggear loops del agente?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA sugirió una arquitectura de tools demasiado compleja para el MVP? ¿El agente
construido alucinó resultados de tools? ¿El system prompt generado por IA producía
razonamiento opaco o loops?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Empezá con el loop ReAct manual antes de usar frameworks — la IA te puede ayudar
a entender el mecanismo paso a paso. El system prompt es el componente más crítico:
invertí tiempo en iterarlo antes de agregar más tools."]
```
