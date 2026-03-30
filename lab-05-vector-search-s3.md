# Lab 05: Semantic Search Benchmark — S3 Vectors vs. Alternativas

**Duración del equipo:** 2–3 personas
**MVP:** 2 semanas | **Extensión:** si hay más tiempo
**Nivel:** Intermedio
**Servicios AWS:** S3 Vectors, Amazon Bedrock (embeddings)

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

**Demo de 15 minutos:**

1. **(0-3 min)** Mostrar el corpus (100+ chunks de documentos técnicos reales o del dataset del lab) y las queries de prueba. Explicar en 60 segundos por qué se necesitan datos propios en lugar de benchmarks genéricos de internet.
2. **(3-10 min)** Ejecutar el benchmark:
   ```
   python vector-bench.py --corpus ./docs --queries queries.txt --runs 3

   Indexando corpus (127 chunks)...
     S3 Vectors: 127 vectores indexados en 23.4s
     Chroma:     127 vectores indexados en 4.1s

   Corriendo 28 queries × 3 runs...
     S3 Vectors: completado
     Chroma:     completado

   Overlap promedio top-5: 87.3%
   Generando reporte → report/report.html
   ```
3. **(10-15 min)** Abrir `report.html` y mostrar la tabla comparativa. Explicar la decisión: para dev/staging y volúmenes < 1M vectores con < 500 queries/día, Chroma es claramente mejor. S3 Vectors tiene sentido en producción con corpus > 1M vectores donde no se quiere gestionar infraestructura.

| Métrica | S3 Vectors | Chroma |
|---------|-----------|--------|
| Latencia p50 | 42ms | 6ms |
| Latencia p95 | 89ms | 14ms |
| Costo (100k vec, 100q/día) | $0.12/mes | $0 |
| Costo (1M vec, 1000q/día) | $12.40/mes | $0 + infra |
| Overlap top-5 | baseline | 87.3% |

---

## 5. Alcance del MVP (2 semanas)

| Semana | Foco | Entregables |
|--------|------|-------------|
| **1** | Corpus + backends funcionando | Corpus de prueba preparado (mínimo 100 chunks). Los dos backends indexan el mismo corpus. Una query corre en ambos y retorna resultados. |
| **2** | Benchmark completo + reporte | CLI completo con todas las queries. Métricas de latencia calculadas. Modelo de costo implementado. Reporte HTML generado. Decisión documentada. |

**Fuera del MVP:** más de dos backends, benchmarking a escala 1M+ vectores, métricas de recall con ground truth etiquetado.

---

## 6. Extensión (si hay más tiempo)

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

## 8. Criterios de evaluación

| Criterio | Métrica mínima de éxito | Peso |
|---|---|---|
| **Benchmark reproducible** | Dos runs producen métricas con < 15% de variación en p50 y p95 | 30% |
| **Métricas de latencia correctas** | p50, p95, p99 calculados sobre mínimo 30 queries por backend; p99 > p95 > p50 siempre | 25% |
| **Modelo de costo documentado** | La tabla incluye fuentes de pricing usadas y supuestos explícitos (dimensionalidad, storage vs. query) | 20% |
| **Overlap de resultados** | La métrica de overlap está calculada y el equipo puede explicar su significado para la decisión | 15% |
| **Decisión documentada** | El equipo presenta una recomendación clara con los datos como respaldo, no como opinión | 10% |

---

## 9. Riesgos y desafíos

- **S3 Vectors es un servicio nuevo** — La documentación puede estar incompleta. El SDK de boto3 requiere una versión específica. Verificar la versión mínima requerida el día 1 y fijarla en `requirements.txt`.
- **Comparación justa entre backends** — Ambos backends deben recibir exactamente los mismos vectores (generados una sola vez y guardados en disco antes de indexar). Si los embeddings se generan dos veces, el overlap medido refleja ruido del proceso, no diferencias reales.
- **Corpus de prueba poco representativo** — Usar documentos que se parezcan al dominio del problema real. Un corpus de artículos genéricos de Wikipedia no es representativo de documentación técnica.
- **Latencia de red vs. latencia del backend** — La latencia medida en S3 Vectors incluye tiempo de red. Documentar desde dónde se corrió el benchmark (laptop local, EC2 en la misma región).
- **Interpretación del overlap** — Un overlap del 87% puede ser excelente o preocupante dependiendo del dominio. Aclarar que el overlap mide consistencia entre backends, no calidad de los resultados.

---

## 10. Reflexión AI (completar al terminar el lab)

Cada equipo completa este template y lo comparte con el programa. Alimenta el loop de mejora entre ciclos.

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

### ¿La IA cambió cómo trabajó el equipo (no solo lo aceleró)?
[ ] Sí — describir: ___
[ ] No, solo aceleró tareas existentes

### Recomendación para el próximo equipo
[ej: "Antes de escribir código, pedirle a Claude que diseñe el protocolo del benchmark —
qué se mide, cómo se controlan las variables. Para la API de S3 Vectors, leer la docs
oficial directamente en lugar de confiar en el código generado por IA."]
```
