# Lab 17: VisualQA — IA Multimodal Aplicada al Desarrollo

> 🇺🇸 [English version](en/lab-17-multimodal.md)

**Equipo:** 2–3 personas
**Nivel:** Intermedio

---

## 1. Nombre del lab

**VisualQA: IA Multimodal Aplicada al Desarrollo** — Usar modelos de visión para automatizar la revisión visual de interfaces, detectar regresiones de UI, y extraer información de imágenes

---

## 2. Skill principal

**IA Multimodal (visión)** — Usar modelos con capacidades de visión para analizar imágenes en contextos de desarrollo: comparar screenshots de UI entre versiones, describir lo que el modelo ve en una interfaz, detectar diferencias visuales, y extraer datos estructurados de imágenes (mockups, wireframes, diagramas). El equipo aprende qué pueden y qué no pueden hacer los modelos de visión en tareas prácticas de desarrollo.

---

## 3. Problema a resolver

El equipo mantiene una aplicación web y cada release requiere revisión visual: verificar que los cambios de código no rompieron la UI en páginas que no se tocaron. Los QA hacen esta revisión manualmente — abren cada página, comparan con la versión anterior, y reportan diferencias. Este proceso es lento, propenso a errores por fatiga visual, y no escala cuando la aplicación tiene decenas de páginas.

Las herramientas de diff visual basadas en píxeles (como Percy o BackstopJS) detectan cualquier cambio — incluyendo diferencias irrelevantes como un timestamp distinto o un avatar que cambió. Lo que el equipo necesita es una revisión que entienda **semánticamente** lo que cambió: "el botón de submit se movió 20px a la derecha" es distinto de "el formulario perdió un campo obligatorio".

El lab guía al equipo a construir una herramienta que usa modelos de visión para hacer revisión visual semántica: no solo detectar que algo cambió, sino describir qué cambió y si parece intencional o una regresión.

---

## 4. Producto objetivo

Una **CLI** que acepta dos conjuntos de screenshots (baseline y current), los analiza con un modelo de visión, y genera un reporte de diferencias visuales con descripciones semánticas de los cambios detectados.

**Validaciones sugeridas:**

- Capturar screenshots de una aplicación web en dos versiones distintas (puede ser cualquier sitio público o un proyecto propio)
- Enviar pares de screenshots (baseline + current) al modelo de visión y verificar que describe los cambios correctamente
- Clasificar los cambios detectados: regresión (probable bug), cambio intencional, diferencia irrelevante (datos dinámicos)
- Generar un reporte con los cambios categorizados, screenshots anotados, y descripción de cada diferencia
- Probar con al menos un caso donde NO hay cambios y verificar que el modelo no inventa diferencias

*En el show & tell, el equipo muestra la herramienta, el reporte generado, y centra la conversación en la calidad de las descripciones del modelo de visión y los falsos positivos/negativos encontrados.*

---

## 5. Alcance del MVP

**Primera fase — Pipeline de captura y análisis:**
- Setup del entorno: Playwright (o Selenium) para capturar screenshots automáticamente de una aplicación web
- Configurar acceso a un modelo con visión (GPT-4o via API, Llama 3.2 Vision via Ollama, o similar)
- Construir el pipeline básico: capturar screenshots de N páginas → enviar cada par (baseline, current) al modelo de visión → pedirle que describa las diferencias
- Experimentar con prompts: ¿qué instrucciones producen las mejores descripciones? ¿El modelo detecta cambios sutiles? ¿Inventa diferencias que no existen?
- Generar un output estructurado por cada par: página, cambios detectados, descripción, severidad estimada

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas. ¿El modelo de visión fue más o menos preciso de lo esperado?

**Segunda fase — Reporte y clasificación:**
- Implementar clasificación de cambios: regresión (el modelo detecta algo que parece un bug), cambio intencional (el modelo detecta algo que parece un rediseño), irrelevante (datos dinámicos, timestamps)
- Generar un reporte HTML con: resumen ejecutivo, lista de cambios por página, screenshots lado a lado, descripción de cada cambio, y severidad
- Agregar extracción de datos de imágenes: dado un screenshot de un formulario, extraer los campos y sus labels en formato estructurado (JSON)
- Probar con al menos 5 pares de screenshots y evaluar la calidad de las descripciones

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo. ¿El prompt engineering para modelos de visión es distinto al de modelos de texto?

---

## 6. Extensión

- Integrar con **CI/CD**: capturar screenshots automáticamente en cada PR y generar el reporte de regresión visual como comentario en el PR
- Agregar **análisis de accesibilidad visual**: que el modelo evalúe contraste, tamaño de texto, y elementos interactivos según WCAG
- Implementar **análisis de mockups**: dado un mockup de Figma (exportado como imagen) y un screenshot de la implementación, que el modelo describa las diferencias entre diseño e implementación
- Agregar **historial de cambios**: almacenar reportes en SQLite para ver la evolución visual de la aplicación a lo largo del tiempo
- Experimentar con **modelos locales de visión** (Llama 3.2 Vision, LLaVA) vs. modelos cloud (GPT-4o) y comparar calidad de descripciones

---

## 7. Stack sugerido

| Componente | Herramienta |
|---|---|
| Captura de screenshots | Playwright (recomendado) o Selenium |
| Modelo de visión | GPT-4o (via API), Llama 3.2 Vision (via Ollama), o similar |
| CLI | Python + Typer |
| Reporte | Jinja2 + HTML |
| Datos de prueba | Cualquier aplicación web pública o proyecto propio |

No requiere infraestructura cloud. Todo corre local (con Ollama para el modelo de visión, o con API key de OpenAI para GPT-4o).

---

## 8. Terreno a explorar

- ¿Qué tan buenos son los modelos de visión actuales para detectar diferencias entre dos screenshots? ¿Detectan cambios sutiles (1px de offset, un color ligeramente distinto)?
- ¿Cómo se escribe un prompt efectivo para un modelo de visión? ¿Es distinto al prompt engineering de texto? ¿Qué instrucciones mejoran la precisión de las descripciones?
- ¿Cuándo el modelo inventa diferencias que no existen (alucinaciones visuales)? ¿Hay patrones que disparan falsos positivos?
- ¿Qué tipo de cambios describe bien el modelo (layout, colores, textos) y cuáles describe mal (spacing sutil, animaciones, estados hover)?
- ¿Un modelo local (Llama 3.2 Vision) produce descripciones comparables a GPT-4o para esta tarea, o hay una diferencia significativa de calidad?
- ¿Cómo se evalúa la calidad de una descripción visual? ¿Se necesita un humano o se puede automatizar con un segundo modelo?
- ¿La revisión visual semántica es más útil que el diff de píxeles para el equipo, o son complementarios?
- ¿La IA asistente ayudó a diseñar los prompts para el modelo de visión, o el equipo tuvo que iterar manualmente? ¿La IA que usaron para construir la herramienta entiende bien cómo funcionan los modelos de visión?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — VisualQA — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Codex / Windsurf | Construir el pipeline y la CLI | |
| Modelo de visión | Analizar screenshots y describir diferencias | |
| Playwright | Capturar screenshots automáticamente | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en construir el pipeline, en diseñar los prompts para el modelo
de visión, en generar el reporte, o en otra tarea? ¿El modelo de visión superó
las expectativas del equipo o se quedó corto?]

### Momento donde la IA no ayudó o introdujo problemas
[¿El modelo de visión inventó diferencias que no existían? ¿No detectó cambios
que eran obvios para un humano? ¿La IA asistente no entendía bien cómo usar
la API de visión del modelo?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Empezá con pares de screenshots donde las diferencias son obvias para calibrar
qué tan bueno es el modelo. Después probá con diferencias sutiles. El prompt
importa mucho — invertí tiempo en iterarlo."]
```
