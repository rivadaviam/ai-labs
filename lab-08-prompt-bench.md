# Lab 08: PromptBench — Evaluación Sistemática de Prompts de Producción

**Equipo:** 2–3 personas
**Nivel:** Intermedio

---

## 1. Nombre del lab

**PromptBench** — Framework de evaluación y comparación de prompts con datasets reales

---

## 2. Skill principal

**Prompt Evaluation + LLM-as-Judge** — Construcción de pipelines de evaluación rigurosos, métricas automáticas, y comparación sistemática de variantes de prompts usando un LLM como evaluador.

---

## 3. Problema a resolver

El equipo tiene un feature de IA en producción (clasificador de tickets de soporte, generador de respuestas, extractor de datos de formularios) y quiere mejorar el prompt. Cada cambio se valida "a ojo" o mostrándoselo a alguien del equipo para ver si "se ve mejor." No existe proceso riguroso: no hay dataset de evaluación, no hay métricas, y no hay forma de saber si un cambio mejoró el caso general o solo el ejemplo que se usó para iterar. Los cambios de prompt van a producción sin red de seguridad.

---

## 4. Producto objetivo

**Una CLI + reporte HTML** que, dado un dataset de pares (input, expected_output) y dos versiones de un prompt, ejecuta evaluación automática y genera un reporte comparativo con: ganador estadístico, casos de regresión detectados, y mejoras por categoría.

**Validaciones sugeridas:**

- Ejecutar `compare` con dos versiones de un prompt y verificar que el reporte identifica correctamente cuál es mejor y en qué casos
- Verificar que las regresiones detectadas son reales: revisar manualmente los inputs que el sistema marca como "losses"
- Comparar los resultados de los 3 modos de evaluación (exact match, LLM-as-judge, similarity) sobre el mismo dataset y analizar discrepancias
- Ejecutar el mismo benchmark dos veces y verificar la reproducibilidad de los resultados

*En el show & tell, el equipo muestra las validaciones que corrió y centra la conversación en decisiones tomadas, problemas encontrados, y lecciones aprendidas.*

---

## 5. Alcance del MVP

**Primera fase — Runner y evaluación básica:**
- CLI con comando `compare` que acepta dos prompts (archivos `.txt`) + dataset + modelo de evaluación
- Dataset format: JSONL con campos `input`, `expected`, `category` (opcional)
- Runner: ejecuta ambos prompts en paralelo sobre todos los ejemplos del dataset con concurrencia controlada
- Al menos 1 modo de evaluación funcionando (exact match o LLM-as-judge)

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas.

**Segunda fase — Reporte HTML y modos de evaluación completos:**
- 3 modos de evaluación seleccionables: exact match, LLM-as-judge, similarity score (embeddings cosine)
- Output: JSON de resultados + reporte HTML con tabla de wins/losses, ejemplos de regresión, y desglose por categoría
- Comando adicional `dataset expand` para auto-generar más casos de prueba a partir de pocos ejemplos (few-shot dataset expansion)

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo.

---

## 6. Extensión

- Modo A/B testing en producción: dividir tráfico entre prompt_v1 y prompt_v2 con logging automático, para evaluar con datos reales
- Dashboard web (Streamlit) con historial de evaluaciones y evolución de métricas a lo largo del tiempo (cada vez que se corre una evaluación queda guardada)
- Integración con CI/CD: GitHub Action que bloquea el deploy si el nuevo prompt tiene una regresión superior al 5% frente al prompt actual
- Evaluadores pre-construidos reutilizables: coherence, tone consistency, factual accuracy, toxicity
- Soporte para evaluación de prompts multi-turn (sistemas de chat, no solo single-turn)

---

## 7. Stack sugerido

**Gratis / open-source:**
- Ollama + Llama3 como LLM-as-judge (local, cero costo)
- sentence-transformers (`all-MiniLM-L6-v2`) para similarity evaluation (100% local)
- asyncio + httpx para llamadas paralelas al LLM
- Pandas + Jinja2 para reporte HTML
- Typer para CLI

**Alternativas cloud:**
- OpenAI GPT-4o mini como judge (muy confiable, ~$0.002 por evaluación de 100 ejemplos)
- Anthropic Claude Haiku
- Weights & Biases (free tier) para logging y dashboard de experimentos

---

## 8. Terreno a explorar

- ¿Cuáles son los sesgos conocidos de un LLM-as-judge? ¿Cómo afecta la elaboración de la respuesta vs. su corrección al score que asigna?
- ¿Cómo se calibra un LLM-judge? ¿Qué significa que concuerde con evaluación humana en un 80%? ¿Qué dice sobre el 20% restante?
- ¿Qué pasa cuando exact match y LLM-judge dan resultados contradictorios? ¿Cómo decidir cuál creer?
- ¿Qué hace que un dataset de evaluación de prompts sea representativo? ¿Cómo afecta la distribución de casos a las métricas finales?
- ¿Cómo se gestiona la concurrencia en llamadas a APIs de LLM sin superar rate limits?
- ¿Qué información necesita un reporte de evaluación para que un PM pueda tomar una decisión sobre qué prompt usar en producción?
- ¿Cuándo la reproducibilidad de los resultados falla? ¿Qué factores introducen variación entre dos runs del mismo benchmark?
- ¿La IA fue útil para generar el dataset de evaluación, o los casos generados eran poco diversos y sesgados hacia el caso feliz?
- ¿El prompt del LLM-as-judge fue diseñado por el equipo o generado por IA? ¿Cómo afectó esa decisión a la calidad de la evaluación?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — PromptBench — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Copilot / Codex | Implementar el runner con asyncio y el parsing de resultados | |
| Claude / ChatGPT | Diseñar el prompt del LLM-judge y generar test cases | |
| Windsurf / Cursor | Construir la CLI y el generador de reporte HTML | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en generar el dataset de prueba, en escribir el prompt del LLM-judge,
en implementar el runner con asyncio, o en diseñar el reporte HTML?]

### Momento donde la IA no ayudó o introdujo problemas
[¿El LLM-judge generado por IA era demasiado indulgente y aprobaba respuestas incorrectas?
¿Los test cases generados eran muy similares entre sí? ¿El código de concurrencia
tenía race conditions?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Usá IA para generar el primer batch de test cases pero revisalos manualmente —
tienden a ser poco diversos. El prompt del LLM-judge es crítico: invertí tiempo en
calibrarlo contra evaluación humana antes de confiar en los resultados."]
```
