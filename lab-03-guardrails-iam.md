# Lab 03: AI Governance Scanner — Auditoría Automática de Compliance de IAM para Bedrock

**Duración del equipo:** 2–3 personas
**MVP:** 2 semanas | **Extensión:** si hay más tiempo
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

## 5. Alcance del MVP (2 semanas)

**Semana 1 — Scanner core:**
- Setup del ambiente de prueba: script que crea 10-15 roles IAM con distintos niveles de compliance
- Implementar el scanner: `list_roles` → filtrar por acceso a Bedrock → verificar condition key
- Output a JSON con el estado de cada rol
- Generación del texto de política correctiva para roles non-compliant

**Semana 2 — Reporte HTML y validación:**
- Template Jinja2 para el reporte HTML con resumen ejecutivo + tabla de roles + sección de fixes
- Comando `--dry-run` que muestra los fixes sin aplicarlos
- Soporte para `--region` para escanear una región específica
- Test de validación: aplicar un fix generado por el scanner y confirmar que el re-scan lo detecta como compliant

---

## 6. Extensión (si hay más tiempo)

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

## 8. Criterios de evaluación

| Criterio | Métrica mínima de éxito | Peso |
|---|---|---|
| **Precisión del scan** | Clasifica correctamente el 100% de los roles en el ambiente de prueba | 35% |
| **Calidad de los fixes** | El JSON de política generado se puede aplicar con `aws iam put-role-policy` sin modificaciones, y el re-scan lo detecta como compliant | 30% |
| **Cobertura** | Detecta permisos de Bedrock tanto en políticas inline como en políticas managed attached | 20% |
| **Usabilidad** | Un nuevo miembro del equipo puede ejecutar el scan completo siguiendo solo el `--help`, sin documentación adicional | 15% |

---

## 9. Riesgos y desafíos

- **Permisos del usuario que corre el scan** — Necesita `iam:ListRoles`, `iam:ListRolePolicies`, `iam:GetRolePolicy`, `iam:ListAttachedRolePolicies`, `iam:GetPolicyVersion`. En cuentas corporativas, estos pueden estar restringidos. Verificar el día 1.
- **Cuentas con muchos roles** — `list_roles` puede devolver miles de roles. Implementar paginación correctamente desde el principio o el scanner perderá roles silenciosamente.
- **Policies con wildcards complejos** — `bedrock:*` o `bedrock:Invoke*` también otorgan acceso a `InvokeModel`. El parser debe cubrir estos casos para evitar falsos negativos de compliance.
- **Múltiples formatos válidos de condition** — La condition puede usar `StringEquals`, `StringLike`, o `ArnEquals` con el ARN. El verificador debe reconocer todas las variantes válidas.
- **Ambiente de prueba insuficientemente representativo** — Crear al menos 2-3 roles con múltiples políticas y estructura compleja para validar el scanner ante casos reales.

---

## 10. Reflexión AI (completar al terminar el lab)

Cada equipo completa este template y lo comparte con el programa. Alimenta el loop de mejora entre ciclos.

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
