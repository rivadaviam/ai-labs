# Lab 00: AI Functions — Tu Primer Prototipo Agentico

> 🇺🇸 [English version](en/lab-00-AI-functions.md)

**Equipo:** 2–3 personas
**Nivel:** Introductorio — punto de entrada al programa

---

## 1. Nombre del lab

**AI Functions: Tu Primer Prototipo Agentico** — Construir un pipeline agentico con funciones descritas en lenguaje natural, post-conditions para autocorrección, y flujos que combinan múltiples funciones

---

## 2. Skill principal

**AI Functions + Structured Outputs + Agentic Flows** — Diseño de funciones ejecutadas por un modelo de IA que devuelven objetos Python estructurados, con post-conditions que validan la salida y disparan ciclos de autocorrección cuando es necesario.

---

## 3. Problema a resolver

El equipo elige **uno** de estos tres escenarios (o propone el suyo propio):

**Escenario A — Log Intelligence**
El equipo tiene logs de servidor (reales anonimizados o sintéticos). Procesarlos manualmente para detectar patrones, clasificar errores por severidad, e identificar las causas raíz toma horas. Nadie lo hace sistemáticamente y los problemas recurrentes se repiten.

**Escenario B — Meeting Intelligence**
El equipo tiene transcripciones de reuniones. Extraer manualmente los action items, sus responsables, las decisiones tomadas, y los temas sin resolver toma 20-30 minutos por reunión y el resultado es inconsistente entre personas.

**Escenario C — Code Quality Reporter**
El equipo tiene un módulo o servicio Python que nadie quiere documentar ni revisar porque es tedioso. Generar docstrings, detectar code smells, y producir un reporte de calidad manualmente lleva horas y siempre queda postergado.

**O su propio escenario:** Un proceso repetitivo en el trabajo del equipo que implique transformar texto o datos no estructurados en output estructurado y accionable.

---

## 4. Producto objetivo

Un **script Python o notebook** que implementa un pipeline agentico con:
- Al menos **2 AI Functions** definidas para el escenario elegido
- **Post-conditions** en cada función que validan la salida y disparan autocorrección cuando falla
- Un **flujo combinado** que encadena las funciones (secuencial o asíncrono)
- Evidencia visible de que una post-condition atrapó al menos un output incorrecto y el modelo lo autocorrigió

**Validaciones sugeridas:**

- Correr el pipeline con al menos 3 inputs distintos y verificar que el output cumple el schema esperado en todos los casos
- Provocar que una post-condition falle (usar un input que genere output incompleto) y verificar que el modelo autocorrige exitosamente
- Comparar el output del pipeline con lo que el equipo hubiera generado manualmente para el mismo input
- Revisar el log de autocorrección: verificar que muestra dónde falló la post-condition, qué prompt se usó para el retry, y cómo cambió el output entre intentos

*En el show & tell, el equipo muestra las validaciones que corrió y centra la conversación en decisiones tomadas, problemas encontrados, y lecciones aprendidas.*

---

## 5. Alcance del MVP

**Primera fase — Primera AI Function funcionando:**
- Setup del entorno: Python 3.12+, instalación de `strands-ai-functions`, acceso al modelo (Codex, GPT-4o-mini, o Amazon Bedrock)
- Definir y ejecutar **una** AI Function para el escenario elegido
- Explorar los ejemplos del repositorio (`meeting_summary.py`, `universal_loader.py`, `stock_report.py`) para entender patrones
- Implementar una post-condition simple que valide el output y retryar manualmente al menos una vez

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas.

**Segunda fase — Pipeline completo:**
- Segunda AI Function que usa el output de la primera
- Post-conditions en ambas funciones con autocorrección automática (no manual)
- Script o notebook que ejecuta el pipeline de punta a punta
- Dataset de prueba preparado (mínimo 3-5 inputs distintos para la demo)
- Evidencia documentada de autocorrecciones: al menos 1 caso donde la post-condition atrapó un output malo y el modelo lo corrigió

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo.

---

## 6. Extensión

- Implementar el pipeline con **funciones asíncronas**: que dos AI Functions corran en paralelo cuando no dependen una de la otra, y medir el impacto en tiempo total
- Agregar una **tercera función** que consolide los outputs anteriores en un reporte final (PDF, markdown, o JSON estructurado)
- Experimentar con **distintos modelos**: correr el mismo pipeline con GPT-4o-mini, Claude Haiku, y Llama3 via Ollama. Comparar calidad de outputs y tasa de activación de post-conditions
- Medir y visualizar las **estadísticas de autocorrección**: tasa de fallo de post-conditions, número de retries por función, tiempo adicional por autocorrección

---

## 7. Stack sugerido

**Opción principal:**

| Componente | Herramienta |
|---|---|
| AI Functions | `strands-ai-functions` (ver README del repositorio) |
| Modelo | GitHub Copilot Codex / GPT-4o-mini via API |
| Python | 3.12+ |
| Entorno | venv o conda |

**Con AWS (opción corporativa):**

| Componente | Servicio |
|---|---|
| Modelo | Amazon Bedrock — Claude Haiku o Titan (bajo costo) |
| AI Functions | `strands-ai-functions` con backend configurado para Bedrock |
| Acceso | Cuenta corporativa AWS — costo < $1 para el lab completo |

**Alternativa local (costo $0):**

| Componente | Herramienta |
|---|---|
| Modelo | Ollama + `llama3.1` o `mistral` (local, sin costo de API) |
| AI Functions | `strands-ai-functions` con backend configurado para Ollama |

---

## 8. Terreno a explorar

- ¿Qué pasa cuando una post-condition es demasiado estricta? ¿Y cuando es demasiado laxa? ¿Cómo encontrar el punto de equilibrio entre ambos extremos?
- ¿Cuántos intentos de autocorrección necesita el modelo antes de rendir o acertar? ¿Hay casos donde nunca converge?
- ¿Cómo afecta la elección del modelo (local vs. API, tamaño) a la consistencia del schema de output?
- ¿Qué hace que un pipeline de dos AI Functions sea más difícil de debuggear que una sola?
- ¿Cómo se diseña una post-condition que sea verificable y específica sin ser frágil ante variaciones menores del output?
- ¿Cuándo conviene arrancar con el caso más simple posible vs. ir directo al escenario completo?
- ¿Qué tipo de inputs de prueba activan las post-conditions? ¿Qué inputs nunca las activan aunque el output sea malo?
- ¿En qué se diferencia usar IA para escribir AI Functions vs. para escribir código convencional? ¿Qué cambia en el flujo de trabajo?
- ¿La IA que usaste para construir el pipeline fue capaz de diseñar buenas post-conditions, o tendió a generar validaciones triviales que nunca fallan?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — AI Functions — [Equipo] — [Escenario elegido]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Codex / Copilot | Escribir el código del pipeline y las post-conditions | |
| Claude / ChatGPT | Diseñar los prompts de las AI Functions | |
| Windsurf / Cursor | Explorar el repositorio y entender los ejemplos | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en escribir las AI Functions, en diseñar las post-conditions, en
debuggear los retries, o en otro punto? ¿La IA que usaste para construir el lab también
usó AI Functions para hacerlo?]

### Momento donde la IA no ayudó o introdujo problemas
[¿El modelo elegido para las AI Functions no respetaba el schema de output? ¿La IA que
te asistió generó post-conditions que nunca se activaban? ¿Hubo un loop infinito de
retries que no terminaba?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Empezá con el escenario más simple posible y un solo input de prueba hasta que
el primer retry funcione. Escalar recién cuando tenés evidencia de autocorrección real,
no antes."]
```
