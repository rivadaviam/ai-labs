# Lab 02: Guardrails Calibration Tool — Medición Sistemática de Falsos Positivos

> 🇺🇸 [English version](en/lab-02-guardrails-tracing.md)

**Equipo:** 2–3 personas
**Nivel:** Intermedio

---

## 1. Nombre del lab

**Guardrails Calibration Tool** — Medición sistemática de falsos positivos y negativos en Bedrock Guardrails

---

## 2. Skill principal

**Evaluación y calibración de sistemas de moderación AI** — Diseño de test suites, análisis de matrices de confusión, y optimización iterativa de políticas de guardrails.

---

## 3. Problema a resolver

Los guardrails de AI se configuran una vez y raramente se revisan. En producción, esto genera dos problemas que coexisten:

- **Falsos positivos:** queries legítimas de usuarios bloqueadas porque el guardrail detecta un patrón superficial (la palabra "cuenta" disparando la política de `BANK_ACCOUNT_NUMBER`)
- **Falsos negativos:** contenido problemático que pasa sin ser bloqueado porque el guardrail no cubre variaciones del patrón original

Sin una herramienta de medición, no hay forma de cuantificar el impacto real. El equipo de soporte reporta "los usuarios se quejan", pero nadie sabe si eso representa el 2% o el 30% de las queries legítimas.

El equipo construirá `guardrail-bench`: una CLI que convierte la calibración de guardrails de una intuición a un proceso medible y repetible.

---

## 4. Producto objetivo

Una CLI `guardrail-bench` que acepta:
- Un archivo JSONL de test cases con schema `{prompt, category, expected_action, policy_type}`
- Un guardrail ID

Y genera un **reporte HTML** con:
- Matriz de confusión por tipo de política (ContentPolicy, SensitiveInformationPolicy, TopicPolicy, PromptAttackPolicy)
- Tasa de falsos positivos y falsos negativos por política
- Tabla de los 10 casos más problemáticos con prompt, acción esperada, acción real, y assessment del guardrail
- Score de accuracy global

**Validaciones sugeridas:**

- Ejecutar la CLI contra el dataset completo y verificar que el reporte HTML se genera correctamente con las matrices de confusión por política
- Identificar al menos un caso de falso positivo real en el reporte y ajustar la configuración del guardrail para reducirlo
- Re-ejecutar tras el ajuste y verificar que la tasa de FP bajó sin introducir nuevos falsos negativos
- Revisar la tabla de casos problemáticos: ¿los prompts listados son realmente problemáticos o son detecciones legítimas?

*En el show & tell, el equipo muestra las validaciones que corrió y centra la conversación en decisiones tomadas, problemas encontrados, y lecciones aprendidas.*

---

## 5. Alcance del MVP

**Primera fase — Dataset y core de la CLI:**
- Crear el dataset de 40 test cases en `financial_qa.jsonl`, cubriendo las 4 políticas
- Implementar el runner: leer JSONL, llamar `ApplyGuardrail` para cada caso, parsear la respuesta
- Calcular la matriz de confusión por política
- Output a JSON con todos los resultados

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas.

**Segunda fase — Reporte HTML y flujo de calibración:**
- Template Jinja2 para el reporte HTML con los 4 widgets de matriz de confusión
- Tabla de top 10 casos problemáticos con sorting por tipo de error
- Comando `--compare reporte_v1.json reporte_v2.json` que genera un diff de mejora entre dos ejecuciones
- Documentación de las opciones de la CLI con `--help`

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo.

---

## 6. Extensión

- Soporte para **múltiples guardrails en paralelo**: comparar la performance de dos configuraciones sobre el mismo dataset
- **Generación automática de adversarial test cases** usando Bedrock: dado un conjunto de prompts bloqueados, generar variaciones que intenten evadir el guardrail
- Modo `--interactive` que permite revisar cada caso problemático y decidir si ajustar el dataset o el guardrail
- Exportar el reporte en **formato CSV** para análisis en herramientas externas

---

## 7. Stack sugerido

**AWS (recomendado):**

| Componente | Servicio |
|---|---|
| Guardrails | Amazon Bedrock Guardrails (`ApplyGuardrail` API) |
| CLI | Python 3.12 + Typer |
| HTTP client | boto3 |
| Templating | Jinja2 |
| Visualización HTML | Chart.js (via CDN) |

Costo estimado: < $1 por equipo (cada llamada a `ApplyGuardrail` cuesta ~$0.0015; 40 casos × 5 ejecuciones = ~$0.30).

**Alternativa sin AWS:**
Reemplazar Bedrock Guardrails por **OpenAI Moderation API** (gratuita). El schema de input/output es distinto pero la lógica de la CLI es idéntica.

---

## 8. Terreno a explorar

- ¿Qué distingue un falso positivo de una detección legítima en un guardrail de contenido? ¿Cómo se diseñan casos de prueba que cubran ambos lados de esa línea?
- ¿Cómo se mide si un cambio de configuración del guardrail mejoró o empeoró la calibración? ¿Qué dice el delta entre v1 y v2?
- ¿Qué hace que un dataset de evaluación sea representativo? ¿Qué pasa si todos los casos son obvios?
- ¿Cuándo un guardrail "perfecto" en el dataset de prueba falla en producción? ¿Qué sesgos introduce la selección manual de casos?
- ¿Cómo presentar métricas de un guardrail a un stakeholder no técnico de forma que pueda tomar decisiones concretas?
- ¿Qué diferencias de comportamiento hay entre políticas de topic, contenido, y PII? ¿Cuáles son más difíciles de calibrar y por qué?
- ¿Qué pasa si el equipo calibra un guardrail que está en producción? ¿Cómo se aísla el trabajo de laboratorio del impacto real?
- ¿La IA fue capaz de generar test cases adversariales útiles, o tendió a generar casos obvios que no desafían al guardrail?
- ¿Cómo usó el equipo IA para diseñar el dataset de evaluación? ¿Qué sesgos introdujo eso en los resultados?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — Guardrails Calibration Tool — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Codex / ChatGPT | Armar el template Jinja2 del reporte HTML | |
| Claude | Diseñar los test cases ambiguos del dataset JSONL | |
| Windsurf / Copilot | Generar el runner de la CLI y el parsing de respuestas | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en generar test cases adversariales, en escribir el código de la CLI,
o en estructurar el reporte HTML? ¿La IA fue capaz de generar prompts que engañaran
al guardrail?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA generó test cases con expected_action incorrecta? ¿Sugirió código que llamaba
a la API de Bedrock con parámetros obsoletos? ¿El reporte HTML generado era
visualmente inutilizable?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Invertí tiempo en el dataset antes de tocar código. Un dataset de 40 casos bien
pensados vale más que una CLI perfecta con 10 casos triviales."]
```
