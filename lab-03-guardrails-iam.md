# Lab 03: AI Governance Scanner — Auditoría Automática de Compliance de IAM para Bedrock

**Duración del equipo:** 2–3 personas
**Nivel:** Intermedio–Avanzado
**Servicios AWS:** IAM, Amazon Bedrock

---

## 1. Nombre del lab

**AI Governance Scanner** — Auditoría automática de compliance de IAM para Bedrock

---

## 2. Skill principal

**AI Governance y seguridad de infraestructura cloud** — Análisis de políticas IAM, detección de gaps de compliance, y generación de remediaciones accionables para enforcement de AI governance.

---

## 3. Problema a resolver

Las políticas de AI governance implementadas "por convención de código" no escalan. Cuando un equipo nuevo crea un microservicio con acceso a Bedrock y olvida incluir el guardrail requerido por la política corporativa, nadie lo detecta hasta un audit manual o un incidente.

El problema estructural: IAM permite hacer `bedrock:InvokeModel` sin ninguna condition key. Si la política corporativa exige que todo llamado incluya el guardrail `bedrock:GuardrailIdentifier`, esa condición debe estar en la política IAM del rol — no en el código del microservicio. Sin enforcement a nivel IAM, cualquier deploy que lo olvide es un gap de compliance invisible.

El equipo construirá `ai-governance-scan`: una herramienta que hace visible el estado de compliance en segundos y entrega los fixes listos para aplicar.

---

## 4. Producto objetivo

Una CLI `ai-governance-scan` que:

1. Enumera todos los IAM roles de la cuenta
2. Identifica qué roles tienen permisos `bedrock:InvokeModel` o `bedrock:Converse`
3. Verifica si tienen la condition `bedrock:GuardrailIdentifier` con el ARN requerido
4. Clasifica cada rol como `COMPLIANT` o `NON_COMPLIANT`
5. Genera el texto exacto de la política correctiva para cada rol no-compliant (copy-paste ready)

**Output:** Reporte HTML + JSON con resumen ejecutivo, estado por rol, y fixes listos para aplicar.

**Demo de 15 minutos:**

1. **(0-2 min)** Mostrar el setup: 15 IAM roles de prueba, algunos con la condition correcta, algunos sin ella
2. **(2-5 min)** Ejecutar `ai-governance-scan --required-guardrail arn:aws:bedrock:us-east-1:123:guardrail/abc123 --output reporte.html`. Mostrar el progreso
3. **(5-9 min)** Abrir `reporte.html`: "15 roles escaneados. 6 compliant ✅, 9 non-compliant ❌." Mostrar la sección de fixes: para el rol `service-payments-lambda-role`, la política correctiva está lista para copiar y pegar
4. **(9-12 min)** Aplicar la corrección a un rol con `aws iam put-role-policy` usando el JSON del reporte (copy-paste directo)
5. **(12-15 min)** Re-ejecutar el scan. Mostrar: "9 non-compliant → 8 non-compliant". El rol corregido ahora aparece como compliant

---

## 5. Alcance del MVP

**Primera fase — Scanner core:**
- Setup del ambiente de prueba: script que crea 10-15 roles IAM con distintos niveles de compliance
- Implementar el scanner: `list_roles` → filtrar por acceso a Bedrock → verificar condition key
- Output a JSON con el estado de cada rol
- Generación del texto de política correctiva para roles non-compliant

**Segunda fase — Reporte HTML y validación:**
- Template Jinja2 para el reporte HTML con resumen ejecutivo + tabla de roles + sección de fixes
- Comando `--dry-run` que muestra los fixes sin aplicarlos
- Soporte para `--region` para escanear una región específica
- Test de validación: aplicar un fix generado por el scanner y confirmar que el re-scan lo detecta como compliant

---

## 6. Extensión

- Escanear **múltiples cuentas** via AWS Organizations: iterar sobre todas las cuentas del org y generar un reporte consolidado
- Modo `--auto-fix`: aplicar las políticas correctivas directamente con confirmación interactiva por rol
- Extender el scanner para verificar **SCPs (Service Control Policies)** a nivel de OU
- **Lambda de scan programático** que corre diariamente y publica el resultado a S3, con notificación si el número de non-compliant roles aumenta

**Alternativa sin AWS (para equipos sin acceso a IAM completo):**
Construir el mismo scanner apuntando a GitHub repos: buscar llamadas a APIs de LLM (`bedrock_client.invoke_model`, `anthropic.messages.create`, `openai.ChatCompletion.create`) que no estén acompañadas de los headers corporativos requeridos o que tengan API keys hardcodeadas. Misma estructura: scanner + clasificación + fixes sugeridos.

---

## 7. Stack sugerido

**AWS (recomendado):**

| Componente | Servicio / Librería |
|---|---|
| Scanner | boto3: `iam.list_roles`, `iam.get_role_policy`, `iam.list_attached_role_policies`, `iam.get_policy_version` |
| CLI | Python 3.12 + Typer |
| Templating | Jinja2 |
| Output | HTML + JSON |

Costo estimado: **$0** — todas las operaciones son llamadas a IAM API sin costo.

**Alternativa open-source (scanner de repos):**

| Componente | Herramienta |
|---|---|
| Scanner | Python con `ast` (Python) o regex (Node/Java) |
| Target | Repos locales o GitHub API |
| Output | Mismo formato HTML + JSON |

---

## 8. Terreno a explorar

- ¿Cómo se detecta acceso a `bedrock:InvokeModel` cuando la política usa wildcards como `bedrock:*` o `bedrock:Invoke*`? ¿Qué otros patrones equivalentes existen?
- ¿Qué significa "compliant" en términos de IAM para Bedrock? ¿Cuándo un permiso amplio es aceptable vs. problemático?
- ¿Cómo se genera una política de remediación que sea mínimamente permisiva sin romper el workload existente?
- ¿Qué ocurre cuando `list_roles` devuelve miles de roles sin paginación? ¿Cómo se pierde información silenciosamente?
- ¿Cuáles son las diferencias prácticas entre políticas inline y managed attached al momento de auditarlas programáticamente?
- ¿Cómo se valida que una corrección de política generada por IA es segura antes de aplicarla en una cuenta real?
- ¿Qué hace que un ambiente de prueba sea suficientemente representativo para confiar en los resultados del scanner?

---

## 9. Reflexión AI (opcional)

Template para documentar el proceso de aprendizaje. No es un entregable obligatorio — se completa si el equipo decide hacerlo o si se acuerda un write-up posterior al show & tell.

```
## Reflexión AI — AI Governance Scanner — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Copilot / Windsurf | Escribir el scanner de políticas IAM y el manejo de paginación | |
| Claude | Generar los casos de prueba IAM y los templates de política correctiva | |
| Codex / ChatGPT | Armar el reporte HTML y el template Jinja2 | |

### Mayor impacto de IA en este lab
[¿La IA fue útil para escribir el parser de políticas IAM (formato JSON anidado complejo)?
¿Ayudó a cubrir los edge cases de wildcards y condiciones múltiples? ¿Generó las
políticas correctivas en el formato correcto?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA generó código que no manejaba la paginación de IAM? ¿Sugirió condiciones IAM
sintácticamente válidas pero semánticamente incorrectas? ¿El parser falló ante políticas
con múltiples statements o Not actions?]

### ¿La IA cambió cómo trabajó el equipo (no solo lo aceleró)?
[ ] Sí — describir: ___
[ ] No, solo aceleró tareas existentes

### Recomendación para el próximo equipo
[ej: "El mayor riesgo es el parser de políticas IAM — antes de escribir código, mapear
a mano todos los formatos posibles de una policy statement con acceso a Bedrock. La IA
cubre el caso feliz pero falla en edge cases si no los describís explícitamente."]
```
