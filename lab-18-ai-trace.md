# Lab 18: AITrace — Análisis de Uso de Agentes de IA en el IDE

**Equipo:** 2–3 personas
**Nivel:** Introductorio–Intermedio

---

## 1. Nombre del lab

**AITrace: Análisis de Uso de Agentes de IA en el IDE** — Parsear y analizar conversaciones de Cascade/Windsurf para entender patrones de uso, identificar ineficiencias, y generar recomendaciones de mejora

---

## 2. Skill principal

**Análisis de datos de uso de IA** — Extraer, estructurar, y analizar los datos que los agentes de IA del IDE generan en cada interacción: prompts del usuario, respuestas del agente, herramientas invocadas, archivos modificados, y timestamps. El equipo aprende a convertir datos crudos de conversaciones en insights accionables sobre cómo el equipo usa (y podría usar mejor) las herramientas de IA.

---

## 3. Problema a resolver

El equipo usa Windsurf/Cascade (o herramientas similares) todos los días, pero no tiene visibilidad sobre sus propios patrones de uso. ¿Cuántas conversaciones inician por día? ¿Qué tipos de tareas le piden al agente? ¿Cuántas iteraciones toma cada tarea? ¿El agente tiene éxito más seguido en tareas de código que en tareas de diseño? ¿Qué herramientas usa más el agente (edit, grep, run_command)? ¿Cuánto contexto consumen las conversaciones largas?

Windsurf almacena las conversaciones localmente en el sistema de archivos. Esos datos son una mina de información sin explotar — contienen el historial completo de cómo cada persona interactúa con el agente: qué pide, qué recibe, qué funciona, y qué no.

La pregunta central del lab es: **¿qué se puede aprender sobre los hábitos de uso de IA del equipo analizando los datos que las herramientas ya generan?**

---

## 4. Producto objetivo

Una **CLI o notebook** que parsea conversaciones de Windsurf/Cascade, extrae datos estructurados, y genera un reporte con métricas y patrones de uso del agente de IA.

**Validaciones sugeridas:**

- Localizar y parsear exitosamente los archivos de conversación de Windsurf/Cascade (entender el formato de almacenamiento)
- Extraer al menos estos datos por conversación: timestamp, número de mensajes, herramientas invocadas por el agente, archivos tocados, y tema/intención del usuario
- Generar métricas agregadas: conversaciones por día, distribución por tipo de tarea, herramientas más usadas, duración promedio de conversación
- Identificar al menos 2 patrones interesantes en los datos (ej: "las conversaciones de debugging toman 3x más iteraciones que las de generación de código", o "el 40% de las conversaciones incluyen al menos un error del agente")
- Generar un reporte visual (HTML o notebook) con gráficos y findings

*En el show & tell, el equipo muestra el reporte de análisis, los patrones descubiertos, y centra la conversación en qué aprendieron sobre sus propios hábitos de uso de IA y qué cambiarían.*

---

## 5. Alcance del MVP

**Primera fase — Explorar y parsear los datos:**
- Localizar dónde Windsurf/Cascade almacena las conversaciones (explorar el sistema de archivos local, identificar el formato — probablemente JSON o SQLite)
- Escribir un parser que extraiga datos estructurados de las conversaciones: mensajes del usuario, respuestas del agente, tool calls (qué herramienta, con qué parámetros), archivos mencionados o editados, timestamps
- Almacenar los datos parseados en SQLite para facilitar consultas
- Hacer consultas exploratorias básicas: ¿cuántas conversaciones hay? ¿cuál es la más larga? ¿qué herramientas aparecen más?
- Documentar el formato de almacenamiento descubierto (esto es ingeniería inversa — es parte del aprendizaje)

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y cómo fue la experiencia de hacer ingeniería inversa del formato de datos. ¿El agente de IA ayudó a entender el formato o no lo conocía?

**Segunda fase — Analytics y reporte:**
- Definir métricas de uso: conversaciones por día/semana, mensajes por conversación, distribución de tipos de tarea (clasificar con heurísticas o con un LLM), herramientas más/menos usadas, ratio de éxito (conversaciones donde el usuario logró lo que quería vs. las que abandonó)
- Construir las visualizaciones: gráficos de actividad temporal, distribución de tipos de tarea, ranking de herramientas, scatter de duración vs. complejidad
- Identificar patrones y anomalías: ¿hay tareas que consistentemente requieren muchas iteraciones? ¿hay herramientas que el agente usa pero el equipo no sabía? ¿hay horas del día donde el agente es más/menos efectivo?
- Generar el reporte final con findings y recomendaciones concretas para el equipo

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo. ¿Los datos revelaron algo sorprendente sobre cómo usan IA?

---

## 6. Extensión

- Agregar **clasificación de intención con LLM**: en lugar de heurísticas, usar un modelo para clasificar automáticamente cada conversación por tipo de tarea (debugging, generación de código, refactor, documentación, diseño, etc.)
- Implementar **análisis de calidad de prompts**: medir qué características de los prompts del usuario (longitud, especificidad, contexto incluido) correlacionan con conversaciones más exitosas
- Construir un **dashboard interactivo** con Streamlit o similar para explorar los datos en tiempo real
- Agregar **comparación entre miembros del equipo** (anonimizada): ¿hay diferencias en cómo distintas personas usan el agente? ¿Alguien descubrió un patrón que otros podrían adoptar?
- Implementar **tracking continuo**: que la herramienta corra periódicamente y genere reportes semanales de uso

---

## 7. Stack sugerido

| Componente | Herramienta |
|---|---|
| Parser | Python (json, sqlite3, pathlib) |
| Almacenamiento | SQLite |
| Visualización | matplotlib + Jinja2 para reporte HTML, o Jupyter notebook |
| CLI | Python + Typer |
| Clasificación (opcional) | LLM para categorizar tipo de tarea |

100% local, sin dependencias externas ni servicios cloud. Los datos nunca salen de la máquina del usuario.

---

## 8. Terreno a explorar

- ¿Dónde almacena Windsurf/Cascade sus conversaciones? ¿El formato es estable o cambia entre versiones? ¿Qué datos están disponibles y cuáles no?
- ¿Qué métricas de uso de IA son realmente útiles para un equipo de desarrollo? ¿Cuáles son vanity metrics (ej: "usamos IA 50 veces esta semana") vs. métricas accionables?
- ¿Cómo se mide si una conversación con el agente fue "exitosa"? ¿Por la última acción del usuario? ¿Por si hubo un commit después? ¿Es posible automatizar esta medición?
- ¿Qué patrones de uso distinguen a alguien que usa IA efectivamente de alguien que no? ¿Se pueden identificar con datos?
- ¿Hay tareas donde el agente es consistentemente bueno o malo? ¿Los datos confirman o contradicen la percepción del equipo?
- ¿Cómo se balancea la utilidad del análisis con la privacidad? ¿Qué datos son sensibles y cómo se manejan?
- ¿La IA asistente ayudó a hacer ingeniería inversa del formato de datos, o no conocía la estructura interna de Windsurf?
- ¿Los findings del análisis cambiaron cómo el equipo usa las herramientas de IA en el día a día?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — AITrace — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Windsurf / Codex | Construir el parser y las visualizaciones | |
| LLM (opcional) | Clasificar tipo de tarea de cada conversación | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en escribir el parser, en diseñar las métricas, en generar las
visualizaciones, o en interpretar los datos? ¿El agente conocía el formato de
almacenamiento de sus propias conversaciones?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA no conocía el formato de datos de Windsurf? ¿Sugirió métricas que no
eran calculables con los datos disponibles? ¿Las clasificaciones automáticas
de tipo de tarea eran incorrectas?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Dedicá tiempo a la fase de exploración del formato de datos — es ingeniería
inversa y no hay documentación. Una vez que tenés los datos en SQLite, las queries
y visualizaciones salen rápido con ayuda de IA."]
```
