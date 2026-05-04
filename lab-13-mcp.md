# Lab 13: ContextBridge — MCP: Usar y Crear Servidores de Contexto

> 🇺🇸 [English version](en/lab-13-mcp.md)

**Equipo:** 2–3 personas
**Nivel:** Intermedio–Avanzado

---

## 1. Nombre del lab

**ContextBridge: Usar y Crear Servidores MCP** — Integrar MCP servers existentes para potenciar un agente de IA, y construir un MCP server propio que expone operaciones específicas de un producto

---

## 2. Skill principal

**Model Context Protocol (MCP)** — Entender, usar, y construir MCP servers que exponen herramientas y datos a agentes de IA en el IDE. El equipo aprende cómo los agentes descubren y usan herramientas externas, qué hace que un MCP server sea útil, y cuándo conviene construir uno propio vs. usar los que ya existen.

---

## 3. Problema a resolver

Un equipo necesita construir una herramienta interna — por ejemplo, un sistema de gestión de incidentes donde los developers registren, busquen, y cierren incidentes con su historial de resolución. El lab guía al equipo a construir esta herramienta **usando MCPs existentes durante el proceso de desarrollo**: configurando MCPs de filesystem, SQLite, y GitHub en su IDE para que el agente de IA tenga acceso directo al código, la base de datos, y el repositorio mientras construyen.

Una vez que la herramienta está funcionando, surge un problema natural: el agente de IA que los ayudó a construirla no puede *usarla* como usuario. No puede consultar incidentes abiertos, no puede buscar en el historial de resolución, no puede actualizar el estado de un incidente — porque no existe un MCP server para esa herramienta. Los MCPs genéricos (filesystem, SQLite) permiten acceder a los archivos y la base de datos en crudo, pero no exponen las operaciones de dominio del producto.

La pregunta es: **¿cómo se construye un MCP server que le dé al agente acceso inteligente a un producto que el equipo acaba de crear?**

El lab propone construir un sistema de gestión de incidentes como producto de referencia. Equipos con otra idea de producto pueden elegir libremente — lo importante es que el producto tenga operaciones de dominio que justifiquen un MCP propio.

---

## 4. Producto objetivo

Una **herramienta funcional** (el sistema de incidentes o el producto elegido) construida con asistencia de MCPs existentes, más un **MCP server propio** que expone las operaciones de dominio de esa herramienta, demostrando que el agente puede interactuar con el producto de forma inteligente.

**Validaciones sugeridas:**

- Configurar al menos 2 MCPs de terceros en el IDE y verificar que el agente los usa durante el desarrollo del producto (ej: consultar la base de datos, leer archivos del proyecto)
- Construir la herramienta funcional con al menos 3-4 operaciones de dominio (ej: crear, listar, buscar, cerrar incidentes)
- Construir el MCP server propio y verificar que el agente lo descubre y puede invocar sus herramientas
- Resolver una tarea concreta que requiera que el agente combine MCPs de terceros con el MCP propio (ej: "¿qué incidentes críticos hay abiertos y cuáles se relacionan con el último deploy?")
- Verificar que el MCP server maneja errores correctamente (parámetros inválidos, datos no encontrados)

*En el show & tell, el equipo muestra el producto construido, el MCP server, la demo del agente usándolos, y centra la conversación en decisiones de diseño y problemas encontrados.*

---

## 5. Alcance del MVP

**Primera fase — Construir el producto usando MCPs existentes:**
- Setup del entorno: configurar el IDE (Windsurf o VS Code + Copilot) para usar MCP servers
- Integrar 2-3 MCPs de terceros: filesystem (acceso a archivos del proyecto), SQLite (consultas directas a la base de datos), y opcionalmente GitHub (para el repositorio)
- Construir el sistema de gestión de incidentes (o el producto elegido) con el agente asistido por MCPs: API REST con 3-4 endpoints + SQLite + datos de ejemplo
- Observar cómo los MCPs cambian la experiencia de desarrollo: ¿el agente consulta la base de datos directamente? ¿lee los archivos del proyecto sin que se los peguen? ¿es más útil que sin MCPs?
- Documentar: qué tareas resolvieron bien los MCPs genéricos durante el desarrollo, y qué operaciones de dominio del producto recién construido no están cubiertas

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y cómo los MCPs de terceros cambiaron (o no) la experiencia de desarrollo.

**Segunda fase — Crear un MCP server para el producto:**
- Diseñar las herramientas del MCP server: qué operaciones de dominio exponer, qué parámetros aceptan, qué devuelven (basarse en la brecha identificada en la fase 1)
- Implementar el MCP server usando el SDK oficial (TypeScript o Python)
- Definir al menos 3 herramientas: una de lectura (ej: `get_critical_incidents`), una de búsqueda (ej: `search_resolution_history`), y una de acción (ej: `update_incident_status`)
- Conectar el MCP server al IDE y verificar que el agente lo descubre y lo usa
- Demo de integración: resolver una tarea que combine MCPs de terceros + el MCP propio (ej: "listar incidentes críticos abiertos y correlacionarlos con los últimos commits del repo")

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo.

---

## 6. Extensión

- Agregar **autenticación y permisos** al MCP server: distintos usuarios ven distintos incidentes según su rol
- Implementar **herramientas de escritura más complejas**: que el agente pueda crear incidentes, asignar responsables, o cerrar incidentes con un resumen generado automáticamente
- Agregar **resources** al MCP server (además de tools): exponer datos estáticos como documentación del sistema, runbooks, o esquemas de la base de datos
- Publicar el MCP server como **paquete instalable** (npm o pip) para que otros equipos puedan usarlo
- Conectar el MCP server a un **sistema real** del equipo: una API de producción, un servicio de monitoreo, o un sistema de tickets

---

## 7. Stack sugerido

**Producto (sistema de incidentes o similar):**

| Componente | Herramienta |
|---|---|
| API | FastAPI (Python) o Express (Node.js) |
| Base de datos | SQLite |
| Lenguaje | Python (recomendado) o el que el equipo prefiera |

**MCPs de terceros sugeridos:**

| MCP | Para qué |
|---|---|
| filesystem | Leer archivos del proyecto (código, configs, docs) |
| sqlite | Consultas SQL directas a la base de datos del sistema |
| github | Correlacionar incidentes con PRs, commits, y deploys |

**IDE:**
- Windsurf (soporte nativo de MCP) o VS Code + extensión compatible

---

## 8. Terreno a explorar

- ¿Qué hace que una herramienta de MCP sea "descubrible" por el agente? ¿Cómo afecta el nombre, la descripción, y el schema de parámetros a que el agente la elija correctamente?
- ¿Cuándo un MCP genérico (ej: SQLite) es suficiente y cuándo se necesita uno específico? ¿Dónde está la línea entre "puedo hacer un query SQL" y "necesito una herramienta de dominio"?
- ¿Qué pasa cuando el agente tiene muchas herramientas disponibles? ¿Elige bien cuál usar o se confunde?
- ¿Cómo se diseñan herramientas MCP que devuelvan información útil sin inundar el contexto del agente?
- ¿Qué errores comete el agente al usar herramientas? ¿Pasa parámetros incorrectos? ¿Inventa herramientas que no existen?
- ¿Cómo se testea un MCP server sin depender del agente? ¿Se puede validar la interfaz de forma independiente?
- ¿Qué tan difícil es pasar de un prototipo de MCP server a algo que otro equipo pueda instalar y usar?
- ¿La IA asistente ayudó a diseñar las herramientas del MCP, o el equipo tuvo que iterar manualmente las descripciones hasta que el agente las usara correctamente?
- ¿Construir un MCP server es más parecido a diseñar una API REST o a diseñar un prompt? ¿Qué habilidades aplican y cuáles son nuevas?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — ContextBridge — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Codex / Windsurf | Construir el producto y el MCP server con asistencia de MCPs | |
| MCPs de terceros | Potenciar al agente durante el desarrollo (filesystem, SQLite, GitHub) | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en construir el producto con MCPs, en diseñar las herramientas del
MCP propio, en implementar el server, o en debuggear la integración con el agente?
¿Los MCPs de terceros cambiaron la experiencia de desarrollo de forma significativa?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA generó tool descriptions que el agente no entendía? ¿El agente confundió
herramientas similares? ¿El SDK tenía breaking changes que la IA no conocía?
¿El agente alucinó herramientas que no existían?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Empezá con los MCPs de terceros para entender cómo el agente descubre y usa
herramientas. No saltes directo a construir el MCP propio — la fase 1 te da la intuición
que necesitás para diseñar buenas tool descriptions."]
```
