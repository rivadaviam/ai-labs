# Lab 07: CodeReviewer AI — El Revisor que Nunca Duerme

> 🇺🇸 [English version](en/lab-07-code-reviewer-ai.md)

**Equipo:** 2–3 personas
**Nivel:** Intermedio

---

## 1. Nombre del lab

**CodeReviewer AI** — Pipeline de revisión de código asistida por LLM como GitHub Action

---

## 2. Skill principal

**LLM Workflows + Structured Outputs** — Diseño de pipelines multi-prompt que producen output estructurado (JSON), con parseo y acción sobre los resultados.

---

## 3. Problema a resolver

El equipo recibe PRs tarde en el día. Los reviewers humanos están ocupados o en otra zona horaria. Los comentarios de revisión son inconsistentes: a veces superficiales, a veces excesivos. No existe un primer pase automatizado que detecte patrones problemáticos (hardcoded secrets, falta de manejo de errores, inyección SQL) antes de que llegue un revisor humano — lo que significa que los revisores gastan tiempo en cosas que un tool podría atrapar.

---

## 4. Producto objetivo

**Un GitHub Action** que, al abrir o actualizar un PR, analiza el diff con un LLM y publica comentarios automáticos directamente en las líneas específicas del código dentro del PR en GitHub.

**Validaciones sugeridas:**

- Abrir un PR con al menos 3 bugs plantados (ej: secret hardcodeado, query SQL sin sanitizar, try/catch vacío) y verificar que el bot publica comentarios en las líneas correctas
- Verificar que cada comentario incluye categoría, explicación, y sugerencia de fix en formato estructurado
- Abrir un PR limpio (sin bugs) y verificar que el bot no genera falsos positivos excesivos
- Probar con un diff grande (>200 líneas) y verificar que el procesamiento por archivo funciona correctamente

*En el show & tell, el equipo muestra las validaciones que corrió y centra la conversación en decisiones tomadas, problemas encontrados, y lecciones aprendidas.*

---

## 5. Alcance del MVP

**Primera fase — Pipeline básico de review:**
- GitHub Action funcional que se activa en eventos `pull_request` (opened, synchronize)
- Extrae el diff del PR usando la GitHub REST API
- Construye un prompt con el diff + contexto (lenguaje del repo, propósito del cambio)
- Llama a un LLM y fuerza output JSON estructurado: `[{file, line, severity, category, message, suggestion}]`

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas.

**Segunda fase — Publicación y robustez:**
- Parsea el JSON y publica comentarios en las líneas específicas del diff via GitHub Review API
- Categorías mínimas: `security`, `error-handling`, `readability`
- Límite de seguridad: si el diff supera N líneas, procesar por archivo individualmente

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo.

---

## 6. Extensión

- Sistema de feedback: los developers marcan comentarios como "útil" / "falso positivo" via emoji reaction en GitHub
- Los feedbacks acumulados alimentan un dataset de evaluación para medir precision del reviewer
- Segundo LLM como meta-evaluador que mide calidad de los comentarios del primero
- Dashboard en Streamlit mostrando: precision@k, categorías más frecuentes, tasa de falsos positivos por tipo de issue
- Archivo `.ai-review.yml` en el repo que permite custom rules y categorías a ignorar

---

## 7. Stack sugerido

**Gratis / open-source:**
- Ollama + Codestral 7B o llama3.1 (cero costo, corre local o en EC2 free tier)
- GitHub Actions (gratis para repos públicos o con minutes de free tier)
- Python + PyGithub + httpx
- Streamlit para dashboard de métricas

**Alternativas cloud:**
- OpenAI GPT-4o mini (muy económico para diffs medianos, ~$0.001/PR)
- Anthropic Claude Haiku
- Groq (latencia muy baja, free tier generoso)

---

## 8. Terreno a explorar

- ¿Qué pasa cuando el diff supera el context window del modelo? ¿Cómo cambia la estrategia según si el límite se supera en un archivo vs. en el PR completo?
- ¿Por qué el LLM alucina números de línea y qué información en el prompt reduce ese comportamiento?
- ¿Cuántas iteraciones de prompt se necesitan típicamente para que los comentarios sean útiles? ¿Qué criterios definen un comentario "útil" vs. ruido?
- ¿Cómo se mide la calidad de un código review automático? ¿Qué hace que un comentario sea accionable?
- ¿Cómo afecta el modelo elegido (local vs. API, tamaño) a la consistencia del output estructurado JSON?
- ¿Qué categorías de issues detecta bien un LLM y cuáles detecta mal comparado con un revisor humano?
- ¿Qué pasa cuando se publican muchos comentarios al mismo tiempo en la GitHub API?
- ¿La IA que usaste para construir el code reviewer produce comentarios similares o distintos a los del code reviewer que construiste? ¿Qué dice eso sobre la tarea?
- ¿Cuántas iteraciones de prompt necesitó el equipo para que el LLM dejara de alucinar números de línea? ¿La IA asistente ayudó a resolver ese problema?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — CodeReviewer AI — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Codex / Copilot | Escribir el GitHub Action y el parsing del diff | |
| Claude / ChatGPT | Diseñar el prompt de revisión y iterar sobre la calidad de los comentarios | |
| Windsurf / Cursor | Integrar con la GitHub Review API y debuggear el Action | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en generar el prompt de revisión, en parsear el diff, en escribir
los tests del Action, o en resolver la alucinación de números de línea?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA alucinó números de línea en el output JSON? ¿Generó código incompatible con
la GitHub Review API? ¿El prompt de revisión generaba comentarios genéricos sin valor?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Usá IA para generar el primer prompt de revisión, pero esperá iterarlo 2-3 veces
antes de que los resultados sean buenos. La alucinación de números de línea es el
problema más común y requiere información específica en el prompt para resolverse."]
```
