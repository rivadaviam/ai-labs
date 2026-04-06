# Lab 16: RedTeam — Seguridad y Red Teaming de Sistemas de IA

**Equipo:** 2–3 personas
**Nivel:** Intermedio

---

## 1. Nombre del lab

**RedTeam: Seguridad y Red Teaming de Sistemas de IA** — Construir un sistema de IA con defensas, atacarlo sistemáticamente para encontrar vulnerabilidades, y automatizar las pruebas de seguridad

---

## 2. Skill principal

**Red teaming de sistemas de IA** — Entender los vectores de ataque más comunes contra sistemas basados en LLMs (prompt injection, jailbreak, system prompt extraction, role manipulation, indirect injection), aprender a categorizarlos y ejecutarlos de forma sistemática, y construir herramientas que automatizan la evaluación de seguridad de un sistema de IA antes de ponerlo en producción.

---

## 3. Problema a resolver

Antes de exponer un sistema de IA a usuarios reales, el equipo necesita verificar que no se puede manipular. Los guardrails ayudan (Labs 02-03), pero no son suficientes — un atacante creativo puede encontrar formas de evadir las defensas que las métricas de FP/FN no capturan. La única forma de saber si un sistema es robusto es intentar romperlo.

El lab guía al equipo a construir un chatbot de soporte técnico con system prompt, guardrails, y reglas de comportamiento, y luego atacarlo sistemáticamente. Construir el target es parte del aprendizaje — entender cómo se construye un sistema de IA es necesario para entender cómo atacarlo, y entender cómo se ataca es necesario para construir sistemas más robustos.

El lab propone un chatbot de soporte técnico como target de referencia. Equipos con otro tipo de sistema de IA en mente (asistente interno, bot de documentación, etc.) pueden elegir libremente — lo importante es que el target tenga un system prompt con reglas de comportamiento que se puedan intentar violar.

---

## 4. Producto objetivo

Un **chatbot target con defensas** + una **CLI `redteam`** que ejecuta una batería de ataques categorizados contra el chatbot y genera un reporte de vulnerabilidades con severidad.

**Validaciones sugeridas:**

- Construir el chatbot target con system prompt, guardrails, y al menos 3 reglas de comportamiento verificables (ej: "no revelar el system prompt", "no generar código malicioso", "no salir del dominio de soporte técnico")
- Ejecutar al menos 5 ataques manuales de distintas categorías y documentar cuáles tuvieron éxito
- Automatizar los ataques en la CLI: al menos 3 categorías (prompt injection, jailbreak, system prompt extraction) con 3-5 variantes cada una
- Implementar evaluación automática de éxito: que la CLI determine si el ataque fue exitoso (usando LLM-as-judge o heurísticas)
- Generar un reporte con vulnerabilidades encontradas, severidad, y sugerencias de mitigación
- Aplicar al menos una mitigación y re-ejecutar los ataques para verificar que la vulnerabilidad se cerró

*En el show & tell, el equipo muestra el chatbot, la demo de ataques exitosos, el reporte de vulnerabilidades, y centra la conversación en qué defensas funcionaron, cuáles no, y qué aprendieron sobre la robustez de los LLMs.*

---

## 5. Alcance del MVP

**Primera fase — Construir el target y hacer red teaming manual:**
- Construir el chatbot target: system prompt con rol (soporte técnico), reglas de comportamiento (qué puede y qué no puede hacer), y guardrails básicos (si el modelo lo soporta)
- Investigar vectores de ataque comunes: prompt injection directa, jailbreak (DAN, role-play), extracción de system prompt, role manipulation, encoding tricks
- Ejecutar ataques manuales contra el chatbot y documentar resultados en una tabla: categoría, prompt de ataque, respuesta, éxito/fallo
- Categorizar las vulnerabilidades encontradas y asignar severidad (crítica, alta, media, baja)

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas. ¿Usaron IA para generar ataques? ¿Funcionó?

**Segunda fase — Automatizar el red teaming:**
- Diseñar la estructura de la suite de ataques: categorías, templates de ataques con variantes, formato de input/output
- Implementar la CLI `redteam`: lee un archivo de ataques, los ejecuta contra el chatbot, evalúa si cada ataque fue exitoso (LLM-as-judge: "¿el chatbot violó alguna de sus reglas?"), y genera un reporte
- Implementar al menos 3 categorías de ataques con 3-5 variantes cada una
- Generar un reporte HTML o Markdown con: resumen ejecutivo, vulnerabilidades por categoría, severidad, prompts de ataque exitosos, y sugerencias de mitigación
- Aplicar al menos una mitigación al chatbot y re-ejecutar para verificar

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo. ¿La IA fue mejor generando ataques o evaluando si un ataque fue exitoso?

---

## 6. Extensión

- Agregar **generación automática de ataques**: en lugar de templates fijos, usar un LLM para generar nuevas variantes de ataques basándose en los que funcionaron
- Implementar **indirect prompt injection**: ataques que se inyectan en los datos que el chatbot procesa (ej: instrucciones ocultas en un documento que el chatbot resume)
- Agregar **ataques de exfiltración**: intentar que el chatbot envíe datos a un endpoint externo o ejecute código
- Construir un **dashboard de seguridad** que muestre la evolución de vulnerabilidades entre versiones del chatbot
- Integrar la CLI `redteam` en el **CI/CD**: que corra automáticamente cuando cambia el system prompt y bloquee el deploy si encuentra vulnerabilidades críticas

---

## 7. Stack sugerido

| Componente | Herramienta |
|---|---|
| Chatbot target | Python + modelo conversacional (OpenAI, Ollama, etc.) |
| CLI de red teaming | Python + Typer |
| Ataques | Archivo JSONL con categorías y variantes |
| Evaluación de éxito | LLM-as-judge (segundo modelo que evalúa si la respuesta violó las reglas) |
| Reporte | Jinja2 + HTML o Markdown |

No requiere infraestructura cloud. Todo corre local.

---

## 8. Terreno a explorar

- ¿Qué vectores de ataque son más efectivos contra LLMs actuales? ¿Prompt injection directa sigue funcionando o los modelos ya la manejan? ¿Qué técnicas nuevas están apareciendo?
- ¿Cómo se evalúa automáticamente si un ataque fue exitoso? ¿LLM-as-judge es confiable para esto o tiene sesgos (ej: no detecta violaciones sutiles)?
- ¿Qué defensas son efectivas y cuáles dan falsa sensación de seguridad? ¿Un system prompt robusto es suficiente o se necesitan guardrails adicionales?
- ¿Cómo se diseña un system prompt que sea útil para el usuario Y resistente a ataques? ¿Hay un trade-off entre usabilidad y seguridad?
- ¿Qué hace que un ataque sea "creativo" vs. "mecánico"? ¿Se pueden automatizar los ataques creativos o requieren intervención humana?
- ¿Cuántos ataques son suficientes para tener confianza en la seguridad del sistema? ¿Hay un framework de cobertura para red teaming de IA?
- ¿La IA asistente ayudó a generar ataques efectivos? ¿Los modelos se resisten a generar prompts adversariales? ¿Hay sesgos en la evaluación automática de éxito?
- ¿Red teaming de IA se parece más a penetration testing tradicional o a QA de software? ¿Qué habilidades aplican y cuáles son nuevas?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — RedTeam — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Codex / Windsurf | Construir el chatbot target y la CLI de red teaming | |
| LLM-as-judge | Evaluar automáticamente si los ataques fueron exitosos | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en construir el chatbot target, en generar variantes de ataques,
en implementar la evaluación automática, o en diseñar las mitigaciones?
¿Usaron IA para atacar a la IA?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA se resistió a generar ataques adversariales? ¿El LLM-as-judge no detectó
violaciones sutiles? ¿La IA sugirió defensas que parecían robustas pero se
rompían fácilmente? ¿El chatbot target era demasiado fácil o difícil de atacar?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Empezá con ataques manuales antes de automatizar — la intuición sobre qué
funciona y qué no se desarrolla probando a mano. El LLM-as-judge necesita un
prompt muy específico para evaluar correctamente si una regla fue violada."]
```
