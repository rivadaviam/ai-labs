# Lab 05: Semantic Search Benchmark — S3 Vectors vs. Alternativas

**Equipo:** 2–3 personas
**Nivel:** Intermedio

---

## 1. Nombre del lab

**Semantic Search Benchmark: S3 Vectors vs. Alternativas** — Construir un harness de benchmarking real para tomar una decisión de arquitectura de vector search justificada con datos medidos

---

## 2. Skill principal

**Evaluación de arquitecturas y benchmarking de sistemas AI** — Instrumentación de sistemas de búsqueda semántica, diseño de experimentos reproducibles, y comunicación de resultados técnicos con datos que respalden una decisión de arquitectura.

---

## 3. Problema a resolver

Un equipo de producto debe elegir la solución de vector search para una nueva feature de búsqueda semántica. Las opciones disponibles son S3 Vectors (serverless, pago por uso, servicio nuevo de AWS), OpenSearch Serverless (~$700/mes mínimo), y Chroma local (gratuito pero sin garantías de escala en producción).

Sin datos propios sobre latencia, costo real a distintos volúmenes, y similitud de resultados entre backends, es imposible justificar la elección ante el equipo de arquitectura. "Le pregunté a ChatGPT y dijo que S3 Vectors es mejor" no es una justificación válida.

---

## 4. Producto objetivo

Un script CLI `vector-bench.py` que:
1. Indexa el mismo corpus de documentos en dos backends (S3 Vectors + Chroma)
2. Corre el mismo conjunto de queries en ambos backends
3. Genera un reporte comparativo con métricas de latencia, costo estimado y overlap de resultados

El reporte incluye:
- Latencia p50 / p95 / p99 por backend (en ms)
- Costo mensual estimado para 10k / 100k / 1M vectores con 10 / 100 / 1000 queries/día
- Overlap de resultados top-5 entre backends (% de documentos coincidentes)
- Gráficos de distribución de latencia

**Validaciones sugeridas:**

- Indexar el mismo corpus en ambos backends y verificar que la cantidad de vectores coincide
- Correr el mismo conjunto de queries en ambos backends y comparar los resultados top-5
- Verificar que las métricas de latencia (p50, p95, p99) son reproducibles entre runs
- Validar el modelo de costos con los precios actuales de los servicios usados
- Generar el reporte HTML y verificar que la tabla comparativa permite tomar una decisión fundamentada

*En el show & tell, el equipo muestra las validaciones que corrió y centra la conversación en decisiones tomadas, problemas encontrados, y lecciones aprendidas.*

---

## 5. Alcance del MVP

**Primera fase — Corpus + backends funcionando:**
- Corpus de prueba preparado (mínimo 100 chunks)
- Los dos backends indexan el mismo corpus
- Una query corre en ambos y retorna resultados

> **Checkpoint de reflexión:** Anotar qué herramientas de IA usaron en esta fase, en qué tareas, y un momento donde la IA no ayudó o causó problemas.

**Segunda fase — Benchmark completo + reporte:**
- CLI completo con todas las queries
- Métricas de latencia calculadas (p50, p95, p99)
- Modelo de costo implementado
- Reporte HTML generado
- Decisión de arquitectura documentada con datos

> **Checkpoint de reflexión:** Anotar si la IA cambió cómo abordaron esta fase vs. la primera, y qué ajustarían en el uso de IA si empezaran de nuevo.

**Fuera del MVP:** más de dos backends, benchmarking a escala 1M+ vectores, métricas de recall con ground truth etiquetado.

---

## 6. Extensión

- **Tercer backend:** Agregar FAISS como comparador adicional (relevante para equipos con EC2 disponible)
- **Recall con ground truth:** Si se pueden etiquetar manualmente los resultados correctos para 10 queries, calcular recall@5 y precision@5
- **Benchmark de indexación incremental:** Medir la latencia de agregar 1 documento nuevo al índice en caliente vs. re-indexar desde cero
- **Modelo de costo interactivo:** Convertir la tabla de costos en un slider en el reporte HTML donde el equipo ingresa sus propios volúmenes
- **Comparación de modelos de embeddings:** Mismo benchmark con `amazon.titan-embed-text-v2:0` vs. `sentence-transformers/all-MiniLM-L6-v2` local, midiendo impacto en latencia, costo y overlap

---

## 7. Stack sugerido

**AWS (recomendado):**

| Servicio | Rol |
|----------|-----|
| S3 Vectors | Vector bucket + vector index (backend primario) |
| Bedrock | `amazon.titan-embed-text-v2:0` para generar embeddings |
| Python + boto3 | Cliente para S3 Vectors y Bedrock |
| ChromaDB | Backend de comparación local (siempre incluido como baseline) |
| Pandas + matplotlib | Análisis de resultados y gráficos |
| Jinja2 | Generación del reporte HTML |

Costo estimado: < $1 para indexar ~10k vectores de prueba y correr 500-1000 queries.

**Alternativa open-source (costo $0):**

| Componente | Herramienta |
|------------|-------------|
| Backend 1 | FAISS (índice en memoria) |
| Backend 2 | ChromaDB |
| Embeddings | `sentence-transformers` (modelo local) |

Con esta alternativa el benchmark corre completamente local — útil para prototipar antes de gastar en AWS.

---

## 8. Terreno a explorar

- ¿Qué significa que dos backends de vector search retornen resultados distintos para la misma query? ¿Cuándo importa el overlap y cuándo no?
- ¿Cómo se mide la latencia de un sistema de búsqueda de forma reproducible? ¿Qué factores externos (red, región, carga) afectan los resultados?
- ¿Qué diferencia hay entre p50, p95 y p99 en términos de experiencia de usuario? ¿Cuándo es relevante cada percentil?
- ¿Cómo se construye un modelo de costos para vector search cuando los factores incluyen dimensionalidad, storage, y cantidad de queries?
- ¿Qué hace que un corpus de prueba sea representativo del caso de uso real? ¿Qué pasa si no lo es?
- ¿Por qué es importante que ambos backends indexen exactamente los mismos vectores para que la comparación sea válida?
- ¿Cómo se traduce un benchmark técnico en una recomendación de decisión concreta para el equipo?
- ¿La IA fue útil para diseñar el protocolo del benchmark (qué medir, cómo controlar variables), o el equipo tuvo que diseñarlo manualmente?
- ¿La IA generó código correcto para la API de S3 Vectors (servicio nuevo), o fue necesario recurrir a la documentación oficial?

---

## 9. Reflexión AI

Síntesis de los checkpoints de reflexión recogidos durante el lab. Se presenta como parte del show & tell.

```
## Reflexión AI — Semantic Search Benchmark — [Equipo]

### Herramientas usadas
| Herramienta | Para qué la usamos | Resultado (1-5) |
|---|---|---|
| Copilot / Codex | Generar el código de los backends y la lógica de percentiles | |
| Claude | Diseñar el modelo de costo y revisar la validez estadística del benchmark | |
| Windsurf | Construir el CLI y el generador de reporte HTML | |

### Mayor impacto de IA en este lab
[¿La IA ayudó a diseñar el experimento para que la comparación sea justa? ¿Generó
el código de cálculo de percentiles correctamente en el primer intento? ¿Sugirió
mejoras al modelo de costo que el equipo no había considerado?]

### Momento donde la IA no ayudó o introdujo problemas
[¿La IA generó código de S3 Vectors usando la API incorrecta (servicio nuevo, puede no
estar en datos de entrenamiento)? ¿Sugirió un modelo de costo con supuestos incorrectos?
¿El código de overlap tenía un bug que inflaba el porcentaje?]

### Cambio en el ciclo de desarrollo
¿La IA cambió *cómo* trabajó el equipo (no solo lo aceleró)? Describir un ejemplo
concreto de una decisión que tomaron diferente porque tenían IA disponible, o explicar
por qué no cambió el flujo.

### Recomendación para el próximo equipo
[ej: "Antes de escribir código, pedirle a Claude que diseñe el protocolo del benchmark —
qué se mide, cómo se controlan las variables. Para la API de S3 Vectors, leer la docs
oficial directamente en lugar de confiar en el código generado por IA."]
```
