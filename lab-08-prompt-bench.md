# Lab 08: PromptBench — Evaluación Sistemática de Prompts de Producción

**Duración del equipo:** 2–3 personas
**MVP:** 1 semana | **Extensión:** 1 mes
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

**Demo en 15 minutos:** Ejecutar `promptbench compare prompt_v1.txt prompt_v2.txt --dataset tickets.jsonl --judge claude`. Output en terminal: tabla mostrando que prompt_v2 mejora accuracy de 67% a 81%, pero introduce 3 regresiones (el sistema los muestra con los inputs específicos que fallaron). Abrir el reporte HTML: heatmap de categorías de error, ejemplos de wins y losses lado a lado, gráfico de score distribution.

---

## 5. Alcance del MVP (1 semana)

- CLI con comando `compare` que acepta dos prompts (archivos `.txt`) + dataset + modelo de evaluación
- Dataset format: JSONL con campos `input`, `expected`, `category` (opcional)
- Runner: ejecuta ambos prompts en paralelo sobre todos los ejemplos del dataset con concurrencia controlada
- 3 modos de evaluación seleccionables: exact match, LLM-as-judge (un LLM evalúa si la respuesta es correcta dado el expected), similarity score (embeddings cosine)
- Output: JSON de resultados + reporte HTML con tabla de wins/losses, ejemplos de regresión, y desglose por categoría
- Comando adicional `dataset expand` para auto-generar más casos de prueba a partir de pocos ejemplos (few-shot dataset expansion)

---

## 6. Extensión (1 mes)

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

## 8. Criterios de evaluación

| Criterio | Métrica mínima de éxito |
|---|---|
| Reproducibilidad | La misma comparación ejecutada dos veces produce resultados con < 5% de variación |
| Velocidad | Dataset de 100 ejemplos evaluado en < 3 minutos |
| Calibración del LLM-judge | El judge concuerda con evaluación humana en ≥ 80% de los casos (usando el dataset de calibración del lab) |
| Detección de regresiones | Identifica correctamente 4 de 5 regresiones plantadas en el dataset de prueba |
| Legibilidad del reporte | Un PM sin background técnico puede leer el reporte HTML y concluir qué prompt es mejor y por qué |

---

## 9. Riesgos y desafíos

- **El LLM-as-judge no es objetivo** — Tiende a favorecer respuestas que suenan más elaboradas independientemente de si son correctas. Solución: el prompt del judge debe incluir rubrica explícita con criterios binarios o escala Likert con descripciones concretas de cada nivel.
- **Dataset de evaluación de mala calidad** — Los equipos crean 10 ejemplos todos del mismo tipo y las métricas no son representativas. Recomendación: usar el dataset base de 50 ejemplos balanceados por categoría que provee el lab.
- **Llamadas en paralelo superan rate limits** — Cuando el dataset es grande y el modelo es de API paga. Solución: implementar semaphore con asyncio para limitar concurrencia configurable por CLI flag.
- **Métricas contradictorias** — Exact match dice que v2 es peor pero LLM-judge dice que es mejor. Los equipos no saben cuál creer. Hay que definir desde el principio cuál es la métrica primaria según el tipo de tarea.

---

## 10. Reflexión AI (completar al terminar el lab)

Cada equipo completa este template y lo comparte con el programa. Alimenta el loop de mejora entre ciclos.

```
## Reflexión AI — PromptBench — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| | | |

### Mayor impacto de IA en este lab
[ej: generar el dataset de prueba, escribir el prompt del LLM-judge, implementar el runner con asyncio]

### Momento donde la IA no ayudó o introdujo problemas
[ej: el judge generado por IA era demasiado indulgente, o los test cases eran muy similares entre sí]

### ¿La IA cambió cómo trabajó el equipo (no solo lo aceleró)?
[ ] Sí — describir: ___
[ ] No, solo aceleró tareas existentes

### Recomendación para el próximo equipo
[ej: "Usá IA para generar el primer batch de test cases pero revisalos manualmente — tienden a ser poco diversos"]
```
