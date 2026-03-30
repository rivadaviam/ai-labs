# Lab 02: Guardrails Calibration Tool — Medición Sistemática de Falsos Positivos

**Duración del equipo:** 2–3 personas
**MVP:** 2 semanas | **Extensión:** si hay más tiempo
**Nivel:** Intermedio
**Servicios AWS:** Bedrock Guardrails (ApplyGuardrail API)

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

**Demo de 15 minutos:**

1. **(0-2 min)** Mostrar el archivo `financial_qa.jsonl` con 40 test cases: 15 queries legítimas de clientes, 10 intentos de injection, 10 queries de topic bloqueado, 5 queries con PII real
2. **(2-6 min)** Ejecutar `guardrail-bench --cases financial_qa.jsonl --guardrail-id abc123 --output reporte_v1.html`. Mostrar el progreso en terminal
3. **(6-9 min)** Abrir `reporte_v1.html`: "SensitiveInformationPolicy: 23% falsos positivos. Top 5 casos problemáticos: queries que contienen 'cuenta' en contexto legítimo"
4. **(9-12 min)** Ir a la consola de Bedrock Guardrails y ajustar: cambiar la acción de `BANK_ACCOUNT_NUMBER` de `BLOCK` a `ANONYMIZE`
5. **(12-15 min)** Re-ejecutar con `--output reporte_v2.html`. Mostrar el diff: FP rate bajó de 23% a 4%. Los FN de PromptAttackPolicy se mantienen en 0% (nada se rompió)

---

## 5. Alcance del MVP (2 semanas)

**Semana 1 — Dataset y core de la CLI:**
- Crear el dataset de 40 test cases en `financial_qa.jsonl`, cubriendo las 4 políticas
- Implementar el runner: leer JSONL, llamar `ApplyGuardrail` para cada caso, parsear la respuesta
- Calcular la matriz de confusión por política
- Output a JSON con todos los resultados

**Semana 2 — Reporte HTML y flujo de calibración:**
- Template Jinja2 para el reporte HTML con los 4 widgets de matriz de confusión
- Tabla de top 10 casos problemáticos con sorting por tipo de error
- Comando `--compare reporte_v1.json reporte_v2.json` que genera un diff de mejora entre dos ejecuciones
- Documentación de las opciones de la CLI con `--help`

---

## 6. Extensión (si hay más tiempo)

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

## 8. Criterios de evaluación

| Criterio | Métrica mínima de éxito | Peso |
|---|---|---|
| **Cobertura del dataset** | El JSONL tiene al menos 8 casos por política, balanceados entre FP y FN potenciales | 25% |
| **Precisión del reporte** | La matriz de confusión calculada coincide con verificación manual de 10 casos al azar | 30% |
| **Utilidad del reporte** | Un stakeholder no técnico entiende qué políticas tienen problemas en < 2 minutos | 20% |
| **Demostración de mejora** | El reporte v2 muestra reducción medible de FP en al menos una política respecto al v1 | 15% |
| **Usabilidad de la CLI** | `--help` documenta todas las opciones; exit code 0 en éxito, 1 en error | 10% |

---

## 9. Riesgos y desafíos

- **Configurar un guardrail realista lleva tiempo** — Crear un guardrail con las 4 políticas activas requiere configuración manual en la consola. Hacer esto el primer día.
- **Dataset sesgado hacia casos fáciles** — Si todos los test cases son obvios, el guardrail va a tener 100% de accuracy y no habrá nada para calibrar. Incluir casos deliberadamente ambiguos: queries legítimas con jerga técnica, PII en contexto educativo.
- **Cambios en el guardrail afectan producción** — Si el equipo ajusta un guardrail que está en producción, puede generar un incidente. Crear un guardrail separado para el lab.
- **La API `ApplyGuardrail` requiere un `source` específico** — El parámetro `source` puede ser `INPUT` o `OUTPUT`. Los test cases deben especificarlo para obtener resultados correctos.
- **Reporte HTML sin diseño claro** — Diseñar la estructura del template en papel antes de implementarlo en Jinja2.

---

## 10. Reflexión AI (completar al terminar el lab)

Cada equipo completa este template y lo comparte con el programa. Alimenta el loop de mejora entre ciclos.

```
## Reflexión AI — Guardrails Calibration Tool — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Copilot / Windsurf | Generar el runner de la CLI y el parsing de respuestas | |
| Claude | Diseñar los test cases ambiguos del dataset JSONL | |
| Codex / ChatGPT | Armar el template Jinja2 del reporte HTML | |

### Mayor impacto de IA en este lab
[¿La IA ayudó más en generar test cases adversariales, en escribir el código de la CLI,
o en estructurar el reporte HTML? ¿La IA fue capaz de generar prompts que engañaran
al guardrail?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA generó test cases con expected_action incorrecta? ¿Sugirió código que llamaba
a la API de Bedrock con parámetros obsoletos? ¿El reporte HTML generado era
visualmente inutilizable?]

### ¿La IA cambió cómo trabajó el equipo (no solo lo aceleró)?
[ ] Sí — describir: ___
[ ] No, solo aceleró tareas existentes

### Recomendación para el próximo equipo
[ej: "Invertí tiempo en el dataset antes de tocar código. Un dataset de 40 casos bien
pensados vale más que una CLI perfecta con 10 casos triviales."]
```
