# Lab 11: KnowledgeBot — RAG with Feedback Loop and Measurable Quality

> 🇪🇸 [Versión en español](../lab-11-knowledge-bot.md)

**Team:** 2–3 people
**Level:** Intermediate–Advanced

---

## 1. Lab name

**KnowledgeBot** — Slack bot or CLI that answers questions about internal documentation with RAG, quality metrics, and continuous improvement

---

## 2. Main skill

**RAG (Retrieval-Augmented Generation) with evaluation** — Not just implementing RAG, but measuring its quality with retrieval metrics (precision@k, MRR), detecting documentation gaps, and building the feedback loop that most tutorials skip.

---

## 3. Problem to solve

The team has 200+ pages of internal technical documentation (ADRs, runbooks, onboarding guides, API specs) scattered across Confluence, Google Drive folders, or Markdown repositories. New developers ask the same questions repeatedly. Veterans don't remember which architectural decisions were made 8 months ago or why. Searching in Confluence is frustrating and the results are irrelevant. There's no way to know if the knowledge base is complete or has gaps.

---

## 4. Target product

**A Slack bot** (or interactive CLI if no workspace is available) that answers questions about internal documentation with exact source citations, and logs every interaction to measure quality and detect gaps.

**Suggested validations:**

- Ask a question whose answer is clearly in the documentation and verify the bot answers with the exact source citation
- Ask a question that has no answer in the docs and verify the bot admits it has no information instead of hallucinating
- Verify the feedback (thumbs up/down) is correctly logged in SQLite
- Review the logs: verify each interaction stores the question, response, retrieved documents, and retrieval score
- Test with at least 5 varied questions to evaluate overall retrieval quality

*In the show & tell, the team shows the validations they ran and centers the conversation on decisions made, problems encountered, and lessons learned.*

---

## 5. MVP scope

**First phase — Working RAG pipeline:**
- Ingestion pipeline: process a folder of Markdown/PDF files, chunking with configurable overlap, embedding generation with a local model
- Vector store: Chroma or FAISS (local, zero cost, no server setup)
- Complete RAG pipeline: query → embed → retrieve top-k chunks → prompt with context → response with citation
- Mandatory source citations: the LLM can only cite documents included in the context, never invent references

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems.

**Second phase — Bot, feedback, and observability:**
- Slack bot with Bolt SDK Python or interactive CLI as fallback
- "I don't know" detection: if the retrieval similarity score is low, the bot admits it doesn't have enough information
- Logging in SQLite: each question + response + retrieved documents + retrieval score
- User feedback: thumbs up/down in Slack or rating in CLI, saved in SQLite

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

---

## 6. Extension

- Retrieval metrics computed over the accumulated feedback dataset: precision@k, MRR, NDCG
- Automatic gap detection: frequent questions with low scores → auto-create issues in Jira/Linear with title "Document: [frequent question]"
- Re-ranking with cross-encoder (sentence-transformers) to improve relevance before sending to the LLM
- Streamlit dashboard with: weekly quality, most useful documents, metric evolution, unanswered questions grouped by topic
- Multi-source support: ingest from Confluence API, GitHub repos, and Google Drive in addition to local files

---

## 7. Suggested stack

**Free / open-source:**
- Ollama + `nomic-embed-text` for embeddings (local, very good quality for technical documentation)
- Ollama + Llama 3.1 8B for response generation
- Chroma DB (local, no server required)
- Slack Bolt SDK for Python (requires a Slack workspace, free to create a test one)
- sentence-transformers `cross-encoder/ms-marco-MiniLM-L-6-v2` for reranking
- SQLite for logging and feedback

**Cloud alternatives:**
- OpenAI `text-embedding-3-small` + GPT-4o mini (~$1-2 for an entire month of demo use)
- Pinecone free tier (100k vectors) as an alternative to Chroma
- Cohere Embed v3 (excellent for technical documentation in English/Spanish)

---

## 8. Terrain to explore

- How do chunk_size and overlap affect retrieval precision? Why is there no universal answer and how do you find the optimal point for a specific corpus?
- What is Precision@k and why is it a useful metric for evaluating retrieval? What are its limitations?
- Why does an LLM hallucinate citations in a RAG system? How can the system prompt reduce this behavior?
- What happens when the embedding model changes after the index is built? How do you detect it and how do you prevent it?
- How do you design a RAG system that recognizes when it doesn't have enough information vs. when it has partial information?
- What's the practical difference between implicit feedback (thumbs up/down) and automatic metrics for improving a RAG?
- What makes a question have no answer in the documentation even though the topic is related?
- Did AI help design the bot's anti-hallucination system prompt, or did the team have to iterate on it manually until it stopped inventing citations?
- In which part of the lab did AI save the most time: ingestion pipeline, RAG prompt design, Slack integration, or building the logging?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — KnowledgeBot — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Copilot | Implementing the ingestion pipeline and Slack bot | |
| Claude / ChatGPT | Designing the anti-hallucination system prompt and generating test questions | |
| Windsurf / Cursor | Building the RAG pipeline and the logging system | |

### Biggest AI impact in this lab
[Did AI help more in writing the anti-hallucination system prompt, implementing the
chunking pipeline, integrating with Slack, or generating test questions for
the dataset?]

### Moment where AI didn't help or introduced problems
[Did the LLM invent document citations despite the system prompt? Did AI suggest chunk
sizes without considering the document type? Did the Slack integration code
use an outdated SDK version?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "The anti-hallucination system depends a lot on the system prompt — ask AI
to help test it with adversarial queries from the start. Chunking is critical:
try different sizes and measure the impact on retrieval before moving on."]
```
