# Lab 15: MultiAgent — Orquestación Multi-Agente para Code Review

**Equipo:** 2–3 personas
**Nivel:** Intermedio–Avanzado

---

## 1. Nombre del lab

**MultiAgent: Orquestación Multi-Agente** — Construir un sistema donde agentes especializados colaboran coordinados por un supervisor para resolver una tarea que tiene múltiples dimensiones

---

## 2. Skill principal

**Orquestación multi-agente** — Diseño de sistemas con múltiples agentes de IA especializados que se comunican entre sí, coordinados por un agente supervisor que decide a quién invocar, resuelve conflictos entre hallazgos, y sintetiza resultados. El equipo aprende cuándo la especialización de agentes aporta valor, cómo se diseña la comunicación entre ellos, y qué patrones de coordinación existen (secuencial, paralelo, jerárquico).

---

## 3. Problema a resolver

Un equipo de desarrollo quiere automatizar la revisión de Pull Requests, pero el problema tiene múltiples dimensiones que un solo prompt no cubre bien: calidad de código (naming, complejidad, duplicación), seguridad (dependencias vulnerables, secrets expuestos, SQL injection), y cobertura de tests (funciones sin tests, edge cases no cubiertos). Un solo agente generalista tiende a dar feedback superficial en todas las dimensiones o a enfocarse solo en una y olvidar las demás.

La pregunta central del lab es: **¿cómo se diseña un sistema donde agentes especializados analizan distintos aspectos de un mismo problema y un supervisor sintetiza sus hallazgos en un resultado coherente?**

El lab propone la revisión de PRs como escenario de referencia. Equipos con otra idea de problema multidimensional pueden elegir libremente — lo importante es que el problema tenga al menos 2 dimensiones claras que justifiquen agentes especializados.

---

## 4. Producto objetivo

Un **sistema multi-agente** (2 agentes especializados + 1 supervisor) que analiza un diff o directorio de código y produce un reporte consolidado con hallazgos categorizados por dimensión.

**Validaciones sugeridas:**

- Verificar que cada agente especializado produce hallazgos relevantes a su dimensión (no se solapan ni repiten)
- Verificar que el supervisor invoca a los agentes correctos según el contenido del diff (ej: si no hay cambios de dependencias, no invocar al agente de seguridad de dependencias)
- Probar con al menos 2 diffs distintos: uno con problemas de calidad y otro con problemas de seguridad — verificar que el sistema se adapta
- Verificar que el reporte final sintetiza sin contradecirse (si un agente dice "el naming es correcto" y otro no lo menciona, el reporte no debe inventar problemas de naming)
- Probar qué pasa si un agente no encuentra hallazgos — el supervisor debe manejar el caso sin inventar contenido

*En el show & tell, el equipo muestra la demo del sistema, el reporte generado, y centra la conversación en decisiones de diseño: cómo dividieron las responsabilidades, cómo se comunican los agentes, y qué problemas encontraron en la coordinación.*

---

## 5. Alcance del MVP

**Primera fase — Un agente especializado funcionando:**
- Setup del entorno: Python + modelo con function calling (via API o local con Ollama)
- Diseñar el primer agente especializado (ej: calidad de código) con su system prompt, tools, y formato de output
- Implementar el agente: recibe un diff o archivos, analiza su dimensión, produce hallazgos estructurados
- Probar el agente con 2-3 diffs de ejemplo y verificar que los hallazgos son relevantes y accionables
- Entender el patrón: un agente = system prompt + tools + output schema. Este patrón se replica en la fase 2.

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas. ¿El agente especializado produce feedback más útil que un prompt genérico?

**Segunda fase — Segundo agente + supervisor:**
- Implementar el segundo agente especializado (ej: seguridad) con el mismo patrón de la fase 1
- Diseñar e implementar el supervisor: decide qué agentes invocar, les pasa el contexto relevante, recibe sus hallazgos, resuelve conflictos o redundancias, y sintetiza un reporte unificado
- Implementar el flujo completo: input (diff) → supervisor → agentes → supervisor → reporte
- Probar el sistema completo con diffs que cubran ambas dimensiones y verificar que el reporte es coherente

**2 agentes + 1 supervisor es el producto completo del MVP**, no el mínimo. Si hay tiempo, agregar un tercer agente es extensión.

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo. ¿Fue más difícil diseñar los agentes o el supervisor?

---

## 6. Extensión

- Agregar un **tercer agente especializado** (ej: cobertura de tests) y verificar cómo escala la coordinación del supervisor
- Implementar **ejecución paralela** de agentes (en lugar de secuencial) y comparar latencia
- Agregar **comunicación entre agentes**: que un agente pueda hacer preguntas a otro (ej: el agente de tests pregunta al de calidad qué funciones son más complejas para priorizarlas)
- Implementar **debate entre agentes**: ante un hallazgo ambiguo, dos agentes argumentan a favor y en contra, y el supervisor decide
- Conectar el sistema a **GitHub** para que analice PRs reales del equipo
- Comparar el output del sistema multi-agente con el output de un solo agente generalista con un prompt largo — ¿cuándo gana cada approach?

---

## 7. Stack sugerido

| Componente | Herramienta |
|---|---|
| Orquestación | LangGraph (recomendado) o implementación manual en Python |
| Modelo | Cualquiera con function calling (GPT-4o mini, Llama 3.1 via Ollama, etc.) |
| CLI | Python + Typer |
| Output | Reporte en Markdown o HTML |
| Datos de prueba | Diffs de código (pueden ser de repositorios open-source o del propio equipo) |

No requiere infraestructura cloud. Todo corre local.

---

## 8. Terreno a explorar

- ¿Cómo se decide qué dimensiones merecen un agente especializado y cuáles se pueden cubrir con un prompt más amplio? ¿Dónde está el límite entre especialización útil y complejidad innecesaria?
- ¿Cómo se diseña el system prompt de un agente especializado para que se mantenga en su dominio y no invada el de otros? ¿Qué pasa cuando los dominios se solapan?
- ¿Qué rol juega el supervisor? ¿Es un router, un sintetizador, un árbitro, o las tres cosas? ¿Cómo cambia la complejidad del supervisor al agregar más agentes?
- ¿Cómo se comunican los agentes? ¿Texto libre, JSON estructurado, o un protocolo definido? ¿Qué formato produce mejores resultados?
- ¿Qué pasa cuando dos agentes se contradicen? ¿El supervisor puede resolver el conflicto o necesita una regla explícita?
- ¿Cuánto contexto necesita cada agente? ¿Es mejor pasarle el diff completo o solo la porción relevante a su especialidad?
- ¿Multi-agente es más lento y caro que un solo agente? ¿Cuándo el overhead se justifica?
- ¿La IA asistente ayudó a diseñar los system prompts de los agentes, o el equipo tuvo que iterar manualmente hasta que cada agente se mantuviera en su dominio?
- ¿Construir un sistema multi-agente es más parecido a diseñar una organización de personas o a diseñar una arquitectura de microservicios? ¿Qué metáfora ayuda más?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — MultiAgent — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Windsurf / Codex | Implementar los agentes y el supervisor | |
| LangGraph / manual | Orquestar el flujo multi-agente | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en diseñar los system prompts de los agentes, en implementar
el supervisor, en definir el formato de comunicación entre agentes, o en debuggear
el flujo cuando los agentes se contradecían o repetían?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA generó system prompts que hacían que los agentes invadieran el dominio
de otros? ¿El supervisor generado por IA sintetizaba mal los hallazgos?
¿LangGraph tenía una API distinta a la que la IA conocía?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Empezá con un solo agente y hacelo funcionar bien antes de agregar el segundo.
El supervisor es el componente más difícil — no subestimes cuánto diseño necesita
para sintetizar hallazgos sin inventar."]
```
