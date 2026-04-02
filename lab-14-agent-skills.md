# Lab 14: SkillForge — Crear y Usar Agent Skills

**Equipo:** 2–3 personas
**Nivel:** Introductorio–Intermedio

---

## 1. Nombre del lab

**SkillForge: Crear y Usar Agent Skills** — Instalar skills existentes del ecosistema, entender el formato SKILL.md, y crear skills propias que empaqueten workflows de desarrollo reutilizables

---

## 2. Skill principal

**Agent Skills** — Formato abierto ([agentskills.io](https://agentskills.io/home)) para extender las capacidades de agentes de IA con conocimiento de dominio y workflows reutilizables. Una skill es un directorio con un `SKILL.md` (frontmatter con name/description + instrucciones en el body), opcionalmente acompañado de `scripts/`, `references/`, y `assets/`. Los agentes descubren skills por su metadata, las activan cuando el task matchea la description, y ejecutan las instrucciones.

---

## 3. Problema a resolver

El equipo de desarrollo tiene workflows que repite constantemente: configurar un nuevo microservicio con la estructura del equipo, hacer code review siguiendo las convenciones internas, debuggear problemas de performance con un checklist específico, o preparar un PR para merge. Cada developer lo hace ligeramente distinto, y cuando usan un agente de IA en el IDE, el agente no conoce las convenciones del equipo — genera código genérico, ignora patrones internos, y no sigue los checklists que el equipo ha construido con el tiempo.

La pregunta es: **¿cómo se empaqueta ese conocimiento tácito del equipo en un formato que un agente de IA pueda descubrir, activar, y ejecutar automáticamente?**

El lab propone un dominio concreto para trabajar: **code review con convenciones de equipo**. El equipo define sus convenciones de revisión (naming, manejo de errores, testing, documentación), las empaqueta como Agent Skills, y valida que el agente las aplique al revisar código. Equipos con otra idea de dominio pueden elegir libremente (setup de microservicios, debugging workflows, preparación de PRs, etc.).

---

## 4. Producto objetivo

Un **set de 2-3 Agent Skills funcionales** que empaquetan workflows de desarrollo del equipo, con evidencia de que un agente compatible las descubre, activa, y ejecuta correctamente.

**Validaciones sugeridas:**

- Instalar al menos 2 skills existentes del ecosistema y verificar que el agente las descubre y activa con el prompt correcto
- Leer el `SKILL.md` de las skills instaladas y documentar: qué hace que la description sea buena o mala, cómo están estructuradas las instrucciones, qué patrones usan
- Crear al menos 2 skills propias siguiendo la especificación y verificar que el agente las descubre (aparecen al listar skills)
- Probar cada skill propia con al menos 3 prompts distintos: uno que debería activarla, uno ambiguo, y uno que no debería activarla — documentar si el agente acierta
- Comparar el output del agente con y sin las skills instaladas para la misma tarea — ¿la skill mejoró la calidad del resultado?

*En el show & tell, el equipo muestra las skills creadas, la demo de activación, y centra la conversación en el proceso de diseño de las instrucciones y las decisiones tomadas.*

---

## 5. Alcance del MVP

**Primera fase — Entender el formato: instalar y explorar skills existentes:**
- Setup del entorno: VS Code + GitHub Copilot en modo Agent, o Windsurf (ambos soportan Agent Skills)
- Instalar 2-3 skills del ecosistema existente en `.agents/skills/` — fuentes recomendadas:
  - [Skills curadas por OpenAI](https://github.com/openai/skills/tree/main/skills/.curated): `security-best-practices`, `doc`, `playwright`, `gh-fix-ci`
  - [Directorio skills.sh](https://skills.sh/): `systematic-debugging`, `test-driven-development`, `code-review`
- Probar cada skill instalada: activarla con un prompt, observar cómo el agente carga las instrucciones, verificar que ejecuta lo indicado
- Leer y analizar los `SKILL.md` de las skills instaladas: estudiar la estructura (frontmatter, body, scripts, references), identificar qué patrones de instrucciones funcionan bien

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y qué observaron sobre cómo el agente descubre y activa skills. ¿Alguna skill no se activó cuando debía? ¿Alguna se activó cuando no debía?

**Segunda fase — Crear skills propias:**
- Definir el dominio de trabajo: code review con convenciones de equipo (o el dominio elegido por el equipo)
- Diseñar 2-3 skills para ese dominio. Ejemplo para code review:
  - `review-naming`: revisa naming conventions del equipo (prefijos, sufijos, casing)
  - `review-error-handling`: verifica patrones de manejo de errores según el estándar del equipo
  - `review-checklist`: ejecuta un checklist completo de revisión pre-merge
- Implementar cada skill: crear el directorio, escribir el `SKILL.md` con frontmatter y body, agregar `scripts/` o `references/` si corresponde
- Probar activación y calidad: verificar que el agente las descubre, las activa con el prompt correcto, y produce output útil
- Iterar las descriptions e instrucciones hasta que la activación sea confiable

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo. ¿Usaron IA para escribir las skills? ¿Funcionó?

---

## 6. Extensión

- Agregar **scripts ejecutables** a las skills: que la skill de code review ejecute un linter custom, o que la skill de debugging corra un profiler automáticamente
- Implementar **progressive disclosure**: skills con `references/` que el agente carga solo cuando necesita más detalle (ej: guía completa de convenciones vs. resumen rápido)
- Crear un **skill de meta-nivel**: una skill que ayude a crear otras skills (un "skill creator" del equipo)
- Evaluar la calidad de las skills usando el framework de [evaluación de agentskills.io](https://agentskills.io/skill-creation/evaluating-skills): medir tasa de activación correcta, calidad del output, y falsos positivos
- Publicar las skills del equipo en un repositorio compartido para que otros equipos las instalen y usen

---

## 7. Stack sugerido

| Componente | Herramienta |
|---|---|
| IDE | VS Code + Codex o Windsurf |
| Formato de skills | Agent Skills spec ([agentskills.io/specification](https://agentskills.io/specification)) |
| Validación | `skills-ref validate ./mi-skill` ([skills-ref library](https://github.com/agentskills/agentskills/tree/main/skills-ref)) |
| Lenguaje de scripts | Bash o Python (para skills con scripts ejecutables) |
| Catálogos de skills | [OpenAI curated](https://github.com/openai/skills/tree/main/skills/.curated), [skills.sh](https://skills.sh/) |

No se requiere infraestructura cloud ni servicios externos. Todo corre local en el IDE.

---

## 8. Terreno a explorar

- ¿Qué hace que una description de skill sea buena? ¿Cuánto detalle necesita para que el agente la active correctamente sin activarla de más?
- ¿Cómo se escribe un `SKILL.md` que sea útil para el agente *y* legible para un humano que quiere auditarla o mejorarla?
- ¿Qué tipo de conocimiento se empaqueta bien en una skill (procedimientos, checklists, convenciones) y qué tipo no (intuición, juicio, contexto implícito)?
- ¿Cuántas skills puede manejar un agente antes de que empiece a confundirse o elegir la skill incorrecta?
- ¿Cómo se itera una skill? ¿Qué feedback loop funciona — probar con prompts variados, leer el trace del agente, comparar output con/sin skill?
- ¿Las skills son portables entre agentes (Copilot, Windsurf)? ¿Se comportan igual o hay diferencias en cómo cada agente las interpreta?
- ¿Qué pasa cuando dos skills cubren dominios que se solapan? ¿El agente elige bien cuál activar?
- ¿La IA asistente ayudó a escribir las instrucciones de las skills, o el equipo tuvo que iterar manualmente hasta que las instrucciones fueran específicas y accionables?
- ¿Es más difícil escribir una buena description (para que el agente la descubra) o buenas instrucciones (para que el agente las ejecute bien)?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — SkillForge — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Copilot / Windsurf | Probar skills instaladas y las que creamos | |
| Codex | Generar borradores de SKILL.md y scripts | |
| skills-ref | Validar formato de las skills creadas | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en escribir las instrucciones de las skills, en diseñar las
descriptions para activación correcta, en generar los scripts, o en otro punto?
¿Usaron IA para crear skills sobre cómo usar IA?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA generó descriptions demasiado genéricas que se activaban con cualquier prompt?
¿Las instrucciones generadas por IA eran demasiado vagas para ser accionables?
¿El agente no descubría las skills o las confundía entre sí?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Empezá instalando y leyendo skills existentes antes de crear las tuyas — entender
qué hace que una skill funcione bien es más fácil viéndolo que leyendo la spec. La
description es el componente más crítico: si no está bien, la skill nunca se activa."]
```
