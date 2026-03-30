# Lab 07: CodeReviewer AI — El Revisor que Nunca Duerme

**Duración del equipo:** 2–3 personas
**MVP:** 1 semana | **Extensión:** 1 mes
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

## 5. Alcance del MVP (1 semana)

- GitHub Action funcional que se activa en eventos `pull_request` (opened, synchronize)
- Extrae el diff del PR usando la GitHub REST API
- Construye un prompt con el diff + contexto (lenguaje del repo, propósito del cambio)
- Llama a un LLM y fuerza output JSON estructurado: `[{file, line, severity, category, message, suggestion}]`
- Parsea el JSON y publica comentarios en las líneas específicas del diff via GitHub Review API
- Categorías mínimas: `security`, `error-handling`, `readability`
- Límite de seguridad: si el diff supera N líneas, procesar por archivo individualmente

---

## 6. Extensión (1 mes)

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

## 8. Criterios de evaluación

| Criterio | Métrica mínima de éxito |
|---|---|
| Cobertura funcional | El Action corre sin errores en el 100% de los PRs de prueba |
| Detección de issues | Al menos 3 de 5 bugs plantados son detectados correctamente |
| Calidad de comentarios | Evaluación ciega de 2 reviewers humanos: promedio ≥ 3.5/5 |
| Latencia | Comentarios publicados en < 90 segundos de abrir el PR |
| Output estructurado | 100% de respuestas del LLM parseadas como JSON válido (sin fallback a texto libre) |

---

## 9. Riesgos y desafíos

- **Diff demasiado grande para el context window** — Los equipos suelen ignorar esto y se encuentran con errores de token limit o reviews de baja calidad. Solución: chunking por archivo, no mandar el diff completo.
- **El LLM inventa números de línea** — Si el prompt no incluye el formato exacto del diff unificado, el modelo alucinará line numbers inválidos y los comentarios no se publican. El prompt debe mostrar el formato `@@ -L,S +L,S @@` y pedir referencias exactas.
- **Rate limits de GitHub API** — Publicar muchos comentarios rápido puede resultar en rate limiting temporal. Agregar retry con exponential backoff.
- **Prompt engineering subestimado** — Los equipos esperan calidad alta en el primer intento. La realidad: se necesitan 2-3 iteraciones del prompt para reducir comentarios vagos o irrelevantes. Planificar ese tiempo.

---

## 10. Reflexión AI (completar al terminar el lab)

Cada equipo completa este template y lo comparte con el programa. Alimenta el loop de mejora entre ciclos.

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
