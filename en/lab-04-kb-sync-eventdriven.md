# Lab 04: Live Knowledge Base Sync Pipeline

> 🇪🇸 [Versión en español](../lab-04-kb-sync-eventdriven.md)

**Team:** 2–3 people
**Level:** Intermediate

---

## 1. Lab name

**Live Knowledge Base Sync Pipeline** — Build and validate an event-driven pipeline that keeps a Bedrock Knowledge Base automatically in sync with changes in S3

---

## 2. Main skill

**Event-driven architecture + AWS Bedrock Knowledge Bases** — Designing asynchronous pipelines with delivery guarantees, integrating with Bedrock ingestion APIs, and validating correctness with measured latency.

---

## 3. Problem to solve

A company has an AI assistant on top of internal documents. The source of truth lives in S3 and is updated several times a day: new policies, revised documents, deleted files. However, the Knowledge Base is re-indexed manually once a week. The result is an assistant that responds with outdated information or that cites documents that no longer exist.

The team must solve this with a fully automatic pipeline and demonstrate it with data: it's not enough for the pipeline to "work" in theory — the test harness must measure the actual time it takes the KB to reflect each change.

---

## 4. Target product

A deployable stack (CDK v2 or SAM) that implements the full pipeline:

```
S3 (PutObject / DeleteObject)
  → S3 Event Notifications
    → SQS (with Dead Letter Queue)
      → Lambda (event processor)
        → Bedrock Knowledge Base
          (IngestKnowledgeBaseDocuments / DeleteKnowledgeBaseDocuments)
```

Plus a Python test harness (`test_sync.py`) that validates the pipeline end-to-end by measuring sync latency for inserts and deletes.

**Suggested validations:**

- Upload a document to S3 and verify the KB reflects it (a query returns content from the new document)
- Measure sync latency: time between the S3 upload and KB availability
- Delete a document from S3 and verify the KB stops returning it
- Review the Lambda's CloudWatch Logs: confirm events are processed correctly and the DLQ is empty
- Run the test harness with at least 3 different documents to confirm consistency

*In the show & tell, the team shows the validations they ran and centers the conversation on decisions made, problems encountered, and lessons learned.*

---

## 5. MVP scope

**First phase — Base infrastructure:**
- Deployable CDK/SAM stack: S3 + Event Notifications, SQS with DLQ, connected Lambda, Bedrock KB created
- Lambda can call `StartIngestionJob` successfully
- Verify that a document uploaded to S3 triggers the full pipeline

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems.

**Second phase — Complete pipeline + test harness:**
- Lambda handles Delete events (`DeleteKnowledgeBaseDocuments`)
- Test harness with polling and latency measurement
- Pipeline works end-to-end for inserts and deletes

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

**Out of MVP scope:** documents > 100 pages, multiple data sources, automatic CloudWatch alarms.

---

## 6. Extension

- **Batch efficiency:** Group multiple S3 events into a single `StartIngestionJob` with SQS batching. Measure whether it reduces the number of jobs without increasing latency.
- **DLQ alarm:** CloudWatch alarm when the DLQ has messages. Simulate a Lambda failure and verify the alarm fires.
- **Historical latency report:** The test harness saves results in JSON and produces a latency chart across multiple runs.
- **Multi-format support:** Add support for `.pdf` and `.docx` in addition to `.md` and `.txt`.

---

## 7. Suggested stack

**AWS (recommended):**

| Service | Role |
|----------|-----|
| S3 + S3 Event Notifications | Document source + event trigger |
| SQS + DLQ | Pipeline buffer with delivery guarantees |
| Lambda (Python 3.12) | Processes events and calls the Bedrock KB API |
| Bedrock Knowledge Bases | Indexing and semantic retrieval |
| Bedrock (Titan Embed v2) | Embeddings model for the KB |
| CloudWatch Logs | Lambda observability |
| CDK v2 or SAM | IaC for reproducible deployment |

Estimated cost: < $2 per team for the entire lab.

**Open-source alternative:**

| Component | Tool |
|------------|-------------|
| Local KB | LlamaIndex + ChromaDB |
| File watcher | Python `watchdog` (monitors a local directory) |
| Query API | FastAPI `/query` endpoint |
| Embeddings | `sentence-transformers` (local model) |

The test harness is practically identical — only the ingestion client changes.

---

## 8. Terrain to explore

- How "real-time" can a Bedrock Knowledge Base be? What's the realistic minimum latency between an S3 change and KB availability?
- How do you observe the full lifecycle of a sync event? What happens if the ingestion job fails silently?
- What makes a delete harder to implement correctly than an insert in an event-driven pipeline?
- What's the role of a Dead Letter Queue in this architecture? What information do logs need to diagnose a failure?
- How does testing strategy change when there's inherent latency in the system under test?
- What's the difference between "the document is in S3" and "the document is retrievable in the KB"? What happens in between?
- How do document size and number of re-runs affect lab cost?
- Was AI useful for generating the full CDK/SAM stack, or did it produce code for older API versions that required manual debugging?
- In which part of the lab did AI save the most time: infrastructure, Lambda logic, or test harness design?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — Live KB Sync Pipeline — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Copilot | Generating the Lambda handler and test harness | |
| Claude | Debugging IAM permissions and understanding the Bedrock KB API | |
| Windsurf | Writing and refactoring the CDK/SAM stack | |

### Biggest AI impact in this lab
[Did AI generate the full CDK stack on the first attempt? Did it help understand the
S3 → SQS → Lambda event model? Did it diagnose an IAM permissions error quickly?]

### Moment where AI didn't help or introduced problems
[Did AI use incorrect parameters for the current Bedrock KB API version?
Did it generate CDK code for an older version? Did the test harness have polling
logic that wouldn't terminate?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Asking Claude to explain Bedrock KB's consistency model before
writing code saves a lot of debugging. Copilot is very useful for Lambda boilerplate
but doesn't know DeleteKnowledgeBaseDocuments well — verify against the official docs."]
```
