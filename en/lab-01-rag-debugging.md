# Lab 01: RAG Health Monitor — Automatic Detection of Silent Degradation

> 🇪🇸 [Versión en español](../lab-01-rag-debugging.md)

**Team:** 2–3 people
**Level:** Intermediate

---

## 1. Lab name

**RAG Health Monitor** — Automatic detection of silent degradation in RAG pipelines

---

## 2. Main skill

**AI systems observability** — Instrumentation, custom metrics, and proactive monitoring of Retrieval-Augmented Generation pipelines.

---

## 3. Problem to solve

RAG pipelines fail in silent ways. An apparently minor change — like an SDK update that switches the embedding model from `amazon.titan-embed-text-v1` to `amazon.titan-embed-text-v2:0` — can cause the system to return "no relevant information found" without producing any errors in the logs. The embedding model used to index documents and the one used to transform queries must be identical: if they diverge, the vectors are mathematically incomparable and retrieval collapses.

This kind of bug is especially dangerous because:
- CloudWatch records no errors (the pipeline technically "works")
- Users receive empty or generic responses without understanding why
- The problem can persist for days or weeks until someone escalates it manually

The team will build the tool that would have detected this problem in minutes, not days.

---

## 4. Target product

An automated monitoring system composed of:
- An **AWS Lambda** that runs RAG pipeline health checks
- An **EventBridge scheduled rule** that triggers the Lambda every 5 minutes
- **Custom CloudWatch metrics** published on each execution
- A **CloudWatch dashboard** with real-time visualization
- A **CloudWatch Alarm** that notifies when degradation is detected

Metrics the system publishes:

| Metric | Type | Description |
|---|---|---|
| `RAG/EmbeddingModelConsistency` | 1/0 | 1 = models match, 0 = mismatch |
| `RAG/RetrievalQualityScore` | Float 0-1 | Average similarity of canary queries |
| `RAG/CanaryLatencyMs` | Int | Response time of the full pipeline |
| `RAG/DocumentsRetrieved` | Int | Number of chunks retrieved per canary |

**Suggested validations:**

- Verify the dashboard shows the 4 metrics correctly when the pipeline is healthy
- Simulate the embedding mismatch bug (change the model ID) and verify the alarm fires
- Verify that `RetrievalQualityScore` drops significantly after the mismatch
- Revert the change and verify the alarm returns to OK on the next execution
- Review the Lambda's CloudWatch Logs to confirm the information is sufficient to diagnose the problem

*In the show & tell, the team shows the validations they ran and centers the conversation on decisions made, problems encountered, and lessons learned.*

---

## 5. MVP scope

**First phase — Infrastructure and core logic:**
- Provision OpenSearch Serverless or Bedrock Knowledge Base with a test corpus (minimum 50 documents)
- Implement the Lambda with the health checks: embedding consistency, canary queries, metric publication
- Define a set of 5-10 canary queries with their expected responses and similarity threshold scores
- Publish the 4 custom metrics to CloudWatch
- EventBridge rule configured for periodic execution

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems.

**Second phase — Observability and validation:**
- CloudWatch dashboard with the main widgets
- CloudWatch Alarms configured over critical metrics
- "Break" script that simulates the embedding mismatch bug
- Markdown runbook documenting how to respond to each alarm

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

---

## 6. Extension

- Add **index staleness** detection: compare the date of the last indexed document against a configurable threshold
- Implement **dynamic canary queries**: generate automatic variations with Bedrock to detect edge cases without hardcoded queries
- Add a **semantic drift** metric: compare this week's average score against the previous week's to detect gradual degradation
- Integrate notifications to **Slack or SNS** with problem details and a link to the runbook
- **Auto-remediation Lambda** that, on detecting mismatch, posts a message to SQS for an operator to approve the rollback

---

## 7. Suggested stack

**AWS (recommended):**

| Component | Service |
|---|---|
| Embeddings | Amazon Bedrock — `amazon.titan-embed-text-v2:0` |
| LLM | Amazon Bedrock — Claude Haiku (low cost) |
| Vector store | Amazon OpenSearch Serverless (`vectorsearch` type) |
| Orchestration | AWS Lambda (Python 3.12) |
| Scheduler | Amazon EventBridge Scheduler (rate: 5 minutes) |
| Observability | Amazon CloudWatch (custom metrics, dashboard, alarms) |
| Config | AWS Systems Manager Parameter Store (model IDs, thresholds) |

Estimated cost: < $2 per team for the entire lab.

**Open-source alternative:**

| Component | Tool |
|---|---|
| Embeddings | Ollama + `nomic-embed-text` |
| LLM | Ollama + `llama3` |
| Vector store | Chroma (local mode) |
| Scheduler | Python with APScheduler or system cron |
| Observability | Prometheus + Grafana (Docker Compose) |

---

## 8. Terrain to explore

- Which metrics are actually useful for detecting silent degradation in a RAG pipeline? What's the difference between embedding consistency and retrieval quality?
- What makes a canary query representative of the domain? How do you know if you're measuring the right thing vs. trivial cases that always pass?
- How are alarm thresholds calibrated to minimize false positives without missing real detections?
- What happens when the similarity score drops gradually vs. suddenly? Does the monitoring system detect both cases equally well?
- How does corpus size affect costs and the viability of continuous monitoring?
- What information does a runbook need to be useful in a real incident vs. one that goes unused?
- Why might an alarm fire without there being a real bug? What does that say about system calibration?
- Was AI useful for designing canary queries and their thresholds, or did it generate trivial cases that wouldn't detect real problems?
- In which lab tasks did AI help most: infrastructure (CDK/SAM), Lambda logic, or monitoring system design?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — RAG Health Monitor — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / ChatGPT | Generating the CDK/SAM for the infra | |
| Claude | Designing the canary queries and similarity thresholds | |
| Windsurf / Copilot | Writing the Lambda handler and check modules | |

### Biggest AI impact in this lab
[Did AI help more in writing the Lambda code, designing the canary queries,
configuring CloudWatch, or somewhere else? How much time do you estimate it saved?]

### Moment where AI didn't help or introduced problems
[Did AI suggest incorrect code for the OpenSearch Serverless API? Did it generate
similarity thresholds that didn't make sense for the corpus? Did it propose architecture
with services that exceeded the budget?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Spend the first day provisioning OpenSearch and verifying the Lambda can
write metrics to CloudWatch before starting the detection logic."]
```
