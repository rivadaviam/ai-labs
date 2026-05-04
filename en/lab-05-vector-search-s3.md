# Lab 05: Semantic Search Benchmark — S3 Vectors vs. Alternatives

> 🇪🇸 [Versión en español](../lab-05-vector-search-s3.md)

**Team:** 2–3 people
**Level:** Intermediate

---

## 1. Lab name

**Semantic Search Benchmark: S3 Vectors vs. Alternatives** — Build a real benchmarking harness to make a vector search architecture decision justified with measured data

---

## 2. Main skill

**Architecture evaluation and benchmarking of AI systems** — Instrumentation of semantic search systems, design of reproducible experiments, and communication of technical results with data that backs an architecture decision.

---

## 3. Problem to solve

A product team has to choose the vector search solution for a new semantic search feature. The available options are S3 Vectors (serverless, pay-per-use, new AWS service), OpenSearch Serverless (~$700/month minimum), and Chroma local (free but with no scale guarantees in production).

Without your own data on latency, real cost at different volumes, and result similarity between backends, it's impossible to justify the choice to the architecture team. "I asked ChatGPT and it said S3 Vectors is better" is not a valid justification.

---

## 4. Target product

A `vector-bench.py` CLI script that:
1. Indexes the same document corpus into two backends (S3 Vectors + Chroma)
2. Runs the same set of queries against both backends
3. Generates a comparative report with latency, estimated cost, and result overlap metrics

The report includes:
- Latency p50 / p95 / p99 per backend (in ms)
- Estimated monthly cost for 10k / 100k / 1M vectors with 10 / 100 / 1000 queries/day
- Top-5 result overlap between backends (% of matching documents)
- Latency distribution charts

**Suggested validations:**

- Index the same corpus into both backends and verify the vector count matches
- Run the same set of queries against both backends and compare the top-5 results
- Verify the latency metrics (p50, p95, p99) are reproducible across runs
- Validate the cost model with current prices of the services used
- Generate the HTML report and verify the comparative table allows a well-founded decision

*In the show & tell, the team shows the validations they ran and centers the conversation on decisions made, problems encountered, and lessons learned.*

---

## 5. MVP scope

**First phase — Corpus + working backends:**
- Test corpus prepared (minimum 100 chunks)
- Both backends index the same corpus
- A query runs against both and returns results

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems.

**Second phase — Complete benchmark + report:**
- Full CLI with all queries
- Latency metrics computed (p50, p95, p99)
- Cost model implemented
- HTML report generated
- Architecture decision documented with data

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

**Out of MVP scope:** more than two backends, benchmarking at 1M+ vector scale, recall metrics with labeled ground truth.

---

## 6. Extension

- **Third backend:** Add FAISS as an additional comparator (relevant for teams with EC2 available)
- **Recall with ground truth:** If correct results can be manually labeled for 10 queries, compute recall@5 and precision@5
- **Incremental indexing benchmark:** Measure the latency of adding 1 new document to a hot index vs. re-indexing from scratch
- **Interactive cost model:** Turn the cost table into a slider in the HTML report where the team enters their own volumes
- **Embedding model comparison:** Same benchmark with `amazon.titan-embed-text-v2:0` vs. local `sentence-transformers/all-MiniLM-L6-v2`, measuring impact on latency, cost, and overlap

---

## 7. Suggested stack

**AWS (recommended):**

| Service | Role |
|----------|-----|
| S3 Vectors | Vector bucket + vector index (primary backend) |
| Bedrock | `amazon.titan-embed-text-v2:0` for generating embeddings |
| Python + boto3 | Client for S3 Vectors and Bedrock |
| ChromaDB | Local comparison backend (always included as baseline) |
| Pandas + matplotlib | Result analysis and charts |
| Jinja2 | HTML report generation |

Estimated cost: < $1 to index ~10k test vectors and run 500-1000 queries.

**Open-source alternative ($0 cost):**

| Component | Tool |
|------------|-------------|
| Backend 1 | FAISS (in-memory index) |
| Backend 2 | ChromaDB |
| Embeddings | `sentence-transformers` (local model) |

With this alternative the benchmark runs entirely locally — useful for prototyping before spending on AWS.

---

## 8. Terrain to explore

- What does it mean for two vector search backends to return different results for the same query? When does overlap matter and when doesn't it?
- How do you measure search system latency in a reproducible way? What external factors (network, region, load) affect results?
- What's the difference between p50, p95, and p99 in user-experience terms? When is each percentile relevant?
- How do you build a cost model for vector search when factors include dimensionality, storage, and number of queries?
- What makes a test corpus representative of the real use case? What happens if it isn't?
- Why is it important that both backends index exactly the same vectors for the comparison to be valid?
- How do you translate a technical benchmark into a concrete decision recommendation for the team?
- Was AI useful for designing the benchmark protocol (what to measure, how to control variables), or did the team have to design it manually?
- Did AI generate correct code for the S3 Vectors API (new service), or was it necessary to fall back on the official docs?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — Semantic Search Benchmark — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Copilot | Generating the backend code and percentile logic | |
| Claude | Designing the cost model and reviewing benchmark statistical validity | |
| Windsurf | Building the CLI and HTML report generator | |

### Biggest AI impact in this lab
[Did AI help design the experiment so the comparison was fair? Did it generate
the percentile calculation code correctly on the first try? Did it suggest
improvements to the cost model the team hadn't considered?]

### Moment where AI didn't help or introduced problems
[Did AI generate S3 Vectors code using the wrong API (new service, may not be in
training data)? Did it suggest a cost model with incorrect assumptions?
Did the overlap code have a bug that inflated the percentage?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Before writing code, ask Claude to design the benchmark protocol —
what's measured, how variables are controlled. For the S3 Vectors API, read the official
docs directly instead of trusting AI-generated code."]
```
