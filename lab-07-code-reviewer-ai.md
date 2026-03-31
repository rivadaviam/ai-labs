# Lab 07: CodeReviewer AI — El Revisor que Nunca Duerme

**Duración del equipo:** 2–3 personas
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

**Demo en 15 minutos:** Abrir un PR con 3 bugs plantados (un secret hardcodeado, una query SQL sin sanitizar, y un bloque try/catch vacío). Mostrar cómo el bot publica comentarios en las líneas exactas del diff con: categoría del problema, explicación, y sugerencia de fix. Mostrar el badge "AI Review: 3 issues found" en el encabezado del PR.

---

## 5. Alcance del MVP

- GitHub Action funcional que se activa en eventos `pull_request` (opened, synchronize)
- Extrae el diff del PR usando la GitHub REST API
- Construye un prompt con el diff + contexto (lenguaje del repo, propósito del cambio)
- Llama a un LLM y fuerza output JSON estructurado: `[{file, line, severity, category, message, suggestion}]`
- Parsea el JSON y publica comentarios en las líneas específicas del diff via GitHub Review API
- Categorías mínimas: `security`, `error-handling`, `readability`
- Límite de seguridad: si el diff supera N líneas, procesar por archivo individualmente

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

---

## 9. Reflexión AI (opcional)

Template para documentar el proceso de aprendizaje. No es un entregable obligatorio — se completa si el equipo decide hacerlo o si se acuerda un write-up posterior al show & tell.

```
## Reflexión AI — CodeReviewer AI — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| | | |

### Mayor impacto de IA en este lab
[¿En qué tarea la IA aportó más? ej: generar el prompt de revisión, parsear el diff, escribir los tests del Action]

### Momento donde la IA no ayudó o introdujo problemas
[ej: alucinó números de línea, generó JSON inválido, sugirió código incompatible con la GitHub API]

### ¿La IA cambió cómo trabajó el equipo (no solo lo aceleró)?
[ ] Sí — describir: ___
[ ] No, solo aceleró tareas existentes

### Recomendación para el próximo equipo
[ej: "Usá IA para generar el primer prompt de revisión, pero esperá iterarlo 2-3 veces antes de que los resultados sean buenos"]
```
