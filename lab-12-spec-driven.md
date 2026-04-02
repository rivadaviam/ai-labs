# Lab 12: SpecFirst — Desarrollo Guiado por Especificación

**Equipo:** 2–3 personas
**Nivel:** Intermedio

---

## 1. Nombre del lab

**SpecFirst: Spec-driven development** — Generar documentos de especificación con IA y usarlos como fuente de verdad para construir un feature de punta a punta

---

## 2. Skill principal

**Spec-Driven Development** — Usar herramientas de IA para generar documentos de especificación estructurados (PRD, user stories, acceptance criteria) y luego usar esos documentos como ancla para que la IA genere código, tests, y validaciones alineados con los requisitos. El equipo evalúa cuánto del output generado se adhiere a lo especificado y dónde diverge.

---

## 3. Problema a resolver

Un equipo recibe un pedido vago: "necesitamos una herramienta interna que permita a los developers registrar y buscar decisiones técnicas (ADRs — Architecture Decision Records) del equipo". No hay PRD, no hay stories, no hay criterios de aceptación. Solo una idea y una conversación informal.

Cuando el equipo salta directo al código, el resultado es impredecible: cada developer interpreta el pedido distinto, el alcance crece sin control, y al final nadie sabe si lo construido resuelve lo que se pidió. La pregunta es: **¿qué pasa si antes de escribir una línea de código, el equipo genera una especificación formal con IA y luego usa esa especificación para guiar toda la construcción?**

---

## 4. Producto objetivo

Una **herramienta funcional de gestión de ADRs** (CLI, web app, o API) construida enteramente a partir de una especificación generada por IA, junto con un **reporte de adherencia** que documenta cuánto del código generado se alineó con la spec y dónde divergió.

**Validaciones sugeridas:**

- Verificar que cada user story de la spec tiene al menos una funcionalidad implementada en el código
- Correr los acceptance criteria de la spec como tests (manuales o automatizados) y documentar cuáles pasan y cuáles no
- Identificar al menos 2 casos donde el código generado por IA divergió de la spec y documentar por qué
- Comparar el esfuerzo de construir con spec vs. la experiencia previa del equipo construyendo sin spec

*En el show & tell, el equipo muestra la spec generada, el producto construido, y el reporte de adherencia. La conversación se centra en el proceso spec→código y las decisiones tomadas.*

---

## 5. Alcance del MVP

**Primera fase — Generar la especificación:**
- Instalar y configurar la herramienta de generación de specs (BMAD es una opción que hemos visto en show-and-tells, pero puede usarse spec-kit u otra herramienta relevante)
- A partir de los lineamientos del lab (descripción del producto, constraints, usuarios objetivo), generar: PRD con alcance y objetivos, 4-6 user stories con acceptance criteria, y una propuesta de arquitectura técnica
- Revisar y refinar la spec generada — el equipo toma decisiones sobre qué incluir en el MVP y qué descartar
- Documentar las decisiones de refinamiento: qué cambió entre la spec original y la versión final

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron para generar la spec, cuántas iteraciones necesitaron, y un momento donde la IA generó algo que no tenía sentido o era demasiado genérico.

**Segunda fase — Construir desde la especificación:**
- Usar la spec como contexto explícito para el agente de IA en el IDE (Windsurf, Copilot): incluir la spec en el proyecto como documento de referencia
- Implementar las user stories priorizadas: el equipo construye la herramienta de ADRs guiándose por la spec
- Generar tests a partir de los acceptance criteria de la spec (el agente debería poder derivar tests directamente de los criterios)
- Producir el reporte de adherencia: para cada user story, documentar si se implementó, si el código se alinea con la spec, y dónde divergió

> **Checkpoint de reflexión:** Anotar si tener la spec cambió cómo interactuaron con la IA al generar código. ¿La IA produjo output más alineado con el objetivo? ¿O ignoró la spec y generó lo que quiso?

---

## 6. Extensión

- Experimentar con **distintos niveles de detalle en la spec**: generar una spec mínima (solo PRD) y una spec detallada (PRD + stories + criterios + arquitectura) y comparar la calidad del código generado en cada caso
- Implementar un **pipeline de validación automática**: un script que parsea la spec y verifica automáticamente qué acceptance criteria se cumplen en el código
- Probar con **distintos modelos o agentes** construyendo desde la misma spec y comparar adherencia — ¿el modelo importa más o la spec importa más?
- Iterar la spec después de la fase 2: usar los hallazgos del reporte de adherencia para refinar la spec y reconstruir — medir si la segunda iteración mejora

---

## 7. Stack sugerido

**Generación de specs:**

| Componente | Herramienta |
|---|---|
| Generación de specs | BMAD (opción principal) — framework para generar PRDs, stories, y specs desde lineamientos |
| Alternativa | Prompt estructurado + Windsurf/Copilot — un template de PRD bien diseñado como prompt |
| Formato de spec | Markdown (versionable, legible, portable) |

**Construcción del producto:**

| Componente | Herramienta |
|---|---|
| IDE con agente | Windsurf o VS Code + Copilot |
| Lenguaje | Python (recomendado) o el que el equipo prefiera |
| Storage | SQLite o archivos markdown (para los ADRs) |
| CLI | Typer o argparse |
| Web (si eligen web) | FastAPI + htmx o Streamlit |

---

## 8. Terreno a explorar

- ¿Qué nivel de detalle en la spec produce mejor código generado por IA? ¿Hay un punto donde más detalle deja de ayudar o incluso confunde al modelo?
- ¿Cómo se diseña una spec que sea útil para humanos *y* para agentes de IA? ¿Son las mismas specs o necesitan formatos distintos?
- ¿La IA tiende a ignorar partes de la spec? ¿Cuáles? ¿Hay patrones en lo que el modelo "olvida" o reinterpreta?
- ¿Qué pasa cuando la spec tiene ambigüedades? ¿El modelo las resuelve razonablemente o las amplifica?
- ¿Los acceptance criteria de la spec sirven como base para tests automatizados, o requieren reformulación significativa?
- ¿Cuánto esfuerzo se invierte en generar y refinar la spec vs. cuánto se ahorra en la fase de construcción? ¿El balance es positivo?
- ¿BMAD (o la alternativa elegida) genera specs que el equipo usaría en un proyecto real, o son demasiado genéricas?
- ¿Cambió la forma en que el equipo interactúa con la IA al tener una spec de referencia? ¿Los prompts fueron distintos? ¿El output fue más predecible?
- ¿Qué decisiones tomó el equipo que no hubiera tomado sin tener la spec? ¿La spec funcionó como restricción útil o como obstáculo?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — SpecFirst — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| BMAD / alternativa | Generar la especificación inicial | |
| Windsurf / Codex | Construir el producto desde la spec | |
| Windsurf / Codex | Generar tests desde acceptance criteria | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en generar la spec, en construir el código desde la spec, en generar
tests desde los acceptance criteria, o en otro punto? ¿Tener una spec cambió la calidad
del output de la IA?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La spec generada fue demasiado genérica y hubo que reescribirla? ¿La IA ignoró partes
de la spec al generar código? ¿Los tests generados desde la spec no cubrían los casos
reales?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Invertí tiempo en refinar la spec antes de empezar a codear. La primera versión
que genera BMAD es un punto de partida, no un producto terminado. El código generado
por la IA es tan bueno como la spec que le das."]
```
