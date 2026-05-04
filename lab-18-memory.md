# Lab 18: MemoryArch — Gestión de Contexto y Memoria en Sistemas de IA

> 🇺🇸 [English version](en/lab-18-memory.md)

**Equipo:** 2–3 personas
**Nivel:** Intermedio

---

## 1. Nombre del lab

**MemoryArch: Gestión de Contexto y Memoria** — Construir un asistente conversacional que mantiene coherencia en conversaciones largas implementando estrategias de memoria más allá del context stuffing

---

## 2. Skill principal

**Gestión de contexto y memoria en sistemas conversacionales de IA** — Entender los límites de la ventana de contexto, experimentar con el degradado de coherencia en conversaciones largas, e implementar estrategias de memoria (summarization, retrieval-based, memoria estructurada) que permitan mantener conversaciones útiles sin depender de meter todo el historial en el prompt. El equipo aprende cuándo cada estrategia funciona, cuánto cuesta, y qué trade-offs implica.

---

## 3. Problema a resolver

El equipo quiere construir un asistente de onboarding que guíe a nuevos developers por el codebase del equipo: explica la arquitectura, responde preguntas sobre convenciones, sugiere por dónde empezar según el rol de la persona. Pero las conversaciones de onboarding son largas — un developer nuevo hace preguntas durante días o semanas, y el asistente necesita recordar qué ya explicó, qué preguntas hizo el usuario, y adaptar sus respuestas según el progreso.

Con un approach naive (meter toda la conversación en el contexto), el asistente funciona bien las primeras 10-15 interacciones. Después empieza a degradar: repite cosas que ya dijo, pierde el hilo de temas anteriores, se vuelve lento, y el costo por mensaje crece linealmente. Eventualmente, la ventana de contexto se llena y el asistente pierde acceso a las primeras interacciones — justo las que contenían la información más importante (el rol del developer, los temas que le interesan, los conceptos que le costaron).

La pregunta central del lab es: **¿cómo se diseña la memoria de un asistente conversacional para que mantenga coherencia a lo largo de muchas interacciones sin depender de una ventana de contexto infinita?**

El lab propone un asistente de onboarding como escenario de referencia. Equipos con otra idea de asistente conversacional (soporte técnico, coaching, documentación interactiva) pueden elegir libremente — lo importante es que el caso requiera conversaciones largas con acumulación de contexto.

---

## 4. Producto objetivo

Un **asistente conversacional** con una estrategia de memoria implementada que demuestra mejor coherencia en conversaciones largas comparado con el approach de context stuffing.

**Validaciones sugeridas:**

- Construir el asistente con context stuffing (baseline) y verificar cuántas interacciones aguanta antes de degradar (repetir información, perder el hilo, o llenar la ventana)
- Implementar al menos una estrategia de memoria y verificar que el asistente mantiene coherencia por más interacciones que el baseline
- Probar con una conversación de al menos 20 turnos y verificar que el asistente en la interacción 20 recuerda información de la interacción 3
- Medir y comparar: tokens consumidos por mensaje, latencia, y coherencia entre baseline y versión con memoria
- Probar un caso adversarial: preguntar algo que contradice información anterior y verificar que el asistente detecta la contradicción

*En el show & tell, el equipo muestra ambas versiones (baseline y con memoria), la demo de una conversación larga, las métricas comparativas, y centra la conversación en los trade-offs de la estrategia elegida.*

---

## 5. Alcance del MVP

**Primera fase — Baseline con context stuffing:**
- Construir el asistente de onboarding: system prompt con rol, conocimiento del codebase del equipo (puede ser documentación real o inventada), y capacidad conversacional
- Alimentar el asistente con información del codebase: arquitectura, convenciones, stack técnico, estructura de directorios (como context en el system prompt o como documentos de referencia)
- Implementar context stuffing: cada mensaje del usuario se agrega al historial completo que se envía al modelo
- Experimentar los límites: hacer conversaciones progresivamente más largas y documentar cuándo degrada — ¿en qué interacción empieza a repetirse? ¿Cuándo pierde contexto de las primeras interacciones? ¿Cuánto crece el costo por mensaje?
- Documentar las métricas del baseline: tokens por mensaje, latencia, y puntos de quiebre de coherencia

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas. ¿Cuántas interacciones aguantó el baseline antes de degradar?

**Segunda fase — Implementar una estrategia de memoria:**
- Elegir UNA estrategia de memoria para implementar. Opciones sugeridas (de más accesible a más compleja):
  - **Summarization**: cada N turnos, resumir la conversación anterior y reemplazar el historial con el resumen. Más simple de implementar, pero pierde detalle.
  - **Sliding window + retrieval**: mantener los últimos N turnos en contexto, y los anteriores en un vector store. Cuando el usuario pregunta algo, buscar turnos relevantes del historial. Más complejo pero preserva detalle.
  - **Memoria estructurada**: extraer hechos de la conversación (ej: "el usuario es frontend developer", "ya entiende la arquitectura de auth") y almacenarlos en una base de datos. Incluir los hechos relevantes en cada prompt. Más robusto pero requiere buena extracción.
- Implementar la estrategia elegida e integrarla con el asistente
- Repetir las pruebas del baseline: misma conversación larga, medir las mismas métricas
- Comparar: ¿la versión con memoria mantiene coherencia por más turnos? ¿A qué costo en tokens/latencia? ¿Pierde información importante?

**Una estrategia bien implementada y comparada es suficiente para el MVP.** Implementar una segunda es extensión.

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo. ¿La IA ayudó a diseñar la estrategia de memoria o el equipo la diseñó solo?

---

## 6. Extensión

- Implementar una **segunda estrategia de memoria** y comparar las tres versiones (baseline, estrategia 1, estrategia 2) con las mismas métricas
- Agregar **memoria persistente entre sesiones**: que el asistente recuerde conversaciones anteriores cuando el usuario vuelve días después
- Implementar **olvido inteligente**: que el asistente descarte información que ya no es relevante (ej: "el usuario ya superó el onboarding de auth, esa información puede archivarse")
- Agregar **meta-memoria**: que el asistente pueda responder "¿qué sabes sobre mí?" y listar lo que recuerda del usuario
- Experimentar con **ventanas de contexto grandes** (modelos con 128k+ tokens) y comparar: ¿una ventana enorme hace innecesaria la estrategia de memoria, o sigue habiendo degradación?

---

## 7. Stack sugerido

| Componente | Herramienta |
|---|---|
| Asistente | Python + modelo conversacional (OpenAI, Ollama, etc.) |
| CLI | Python + Typer o Rich para interfaz conversacional |
| Vector store (si eligen retrieval) | Chroma o FAISS (local, sin servidor) |
| Memoria estructurada (si eligen esa vía) | SQLite |
| Métricas | Conteo de tokens (tiktoken), timestamps para latencia |

No requiere infraestructura cloud. Todo corre local.

---

## 8. Terreno a explorar

- ¿Cuántas interacciones aguanta un LLM con context stuffing antes de degradar? ¿Depende del modelo, del tipo de conversación, o de ambos?
- ¿Cómo se mide "coherencia" en una conversación larga? ¿Es subjetivo o se puede automatizar (ej: preguntarle al modelo sobre información de turnos anteriores)?
- ¿Qué pierde cada estrategia de memoria? ¿Summarization pierde detalle? ¿Retrieval pierde el orden temporal? ¿Memoria estructurada pierde matices?
- ¿Cuánto contexto es "suficiente" para cada mensaje? ¿Hay un punto óptimo entre demasiado poco (el modelo no tiene información) y demasiado (el modelo se confunde con tanto contexto)?
- ¿La estrategia de memoria cambia la personalidad o el tono del asistente? ¿Un resumen distorsiona la relación con el usuario?
- ¿Qué pasa cuando la memoria contiene información incorrecta o desactualizada? ¿Cómo se corrige?
- ¿La IA asistente ayudó a diseñar la estrategia de memoria o fue necesario investigar y diseñar manualmente?
- ¿Los modelos con ventanas de contexto largas (128k+) hacen innecesarias estas estrategias, o simplemente posponen el problema?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — MemoryArch — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Codex / Windsurf | Construir el asistente y la estrategia de memoria | |
| Chroma / FAISS (si aplica) | Vector store para retrieval-based memory | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en construir el asistente baseline, en diseñar la estrategia
de memoria, en implementar el vector store/summarization, o en analizar las
métricas comparativas? ¿La IA sugirió una estrategia que el equipo no había
considerado?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA sugirió una estrategia de memoria demasiado compleja para el tiempo
disponible? ¿El asistente con memoria perdía información importante que el
baseline no perdía? ¿La IA no entendía los trade-offs entre estrategias?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Dedicá tiempo a experimentar los límites del baseline antes de implementar
memoria — ver exactamente cuándo y cómo degrada te da la intuición para elegir
la estrategia correcta. Summarization es la más accesible para empezar."]
```
