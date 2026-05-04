# Lab 18: MemoryArch — Context and Memory Management in AI Systems

> 🇪🇸 [Versión en español](../lab-18-memory.md)

**Team:** 2–3 people
**Level:** Intermediate

---

## 1. Lab name

**MemoryArch: Context and Memory Management** — Build a conversational assistant that maintains coherence in long conversations by implementing memory strategies beyond context stuffing

---

## 2. Main skill

**Context and memory management in AI conversational systems** — Understand the limits of the context window, experiment with coherence degradation in long conversations, and implement memory strategies (summarization, retrieval-based, structured memory) that allow useful conversations to be maintained without depending on stuffing the entire history into the prompt. The team learns when each strategy works, how much it costs, and what trade-offs it implies.

---

## 3. Problem to solve

The team wants to build an onboarding assistant that guides new developers through the team's codebase: it explains the architecture, answers questions about conventions, suggests where to start based on the person's role. But onboarding conversations are long — a new developer asks questions for days or weeks, and the assistant needs to remember what it has already explained, what questions the user asked, and adapt its responses based on progress.

With a naive approach (stuffing the entire conversation into the context), the assistant works well for the first 10-15 interactions. After that it starts to degrade: it repeats things it has already said, loses the thread of previous topics, becomes slow, and the cost per message grows linearly. Eventually, the context window fills up and the assistant loses access to the first interactions — exactly the ones that contained the most important information (the developer's role, the topics they're interested in, the concepts that were hardest for them).

The central question of the lab is: **how do you design the memory of a conversational assistant so it maintains coherence across many interactions without depending on an infinite context window?**

The lab proposes an onboarding assistant as the reference scenario. Teams with another idea of conversational assistant (technical support, coaching, interactive documentation) can choose freely — the important thing is that the case requires long conversations with context accumulation.

---

## 4. Target product

A **conversational assistant** with a memory strategy implemented that demonstrates better coherence in long conversations compared to the context stuffing approach.

**Suggested validations:**

- Build the assistant with context stuffing (baseline) and verify how many interactions it endures before degrading (repeating information, losing the thread, or filling the window)
- Implement at least one memory strategy and verify the assistant maintains coherence for more interactions than the baseline
- Test with a conversation of at least 20 turns and verify the assistant in interaction 20 remembers information from interaction 3
- Measure and compare: tokens consumed per message, latency, and coherence between baseline and version with memory
- Test an adversarial case: ask something that contradicts previous information and verify the assistant detects the contradiction

*In the show & tell, the team shows both versions (baseline and with memory), the demo of a long conversation, the comparative metrics, and centers the conversation on the trade-offs of the chosen strategy.*

---

## 5. MVP scope

**First phase — Baseline with context stuffing:**
- Build the onboarding assistant: system prompt with role, knowledge of the team's codebase (can be real or invented documentation), and conversational capability
- Feed the assistant with codebase information: architecture, conventions, technical stack, directory structure (as context in the system prompt or as reference documents)
- Implement context stuffing: each user message is added to the full history sent to the model
- Experiment with the limits: have progressively longer conversations and document when it degrades — at which interaction does it start repeating? When does it lose context from the first interactions? How much does the cost per message grow?
- Document the baseline metrics: tokens per message, latency, and coherence break points

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems. How many interactions did the baseline endure before degrading?

**Second phase — Implement a memory strategy:**
- Choose ONE memory strategy to implement. Suggested options (from most accessible to most complex):
  - **Summarization**: every N turns, summarize the previous conversation and replace the history with the summary. Simpler to implement, but loses detail.
  - **Sliding window + retrieval**: keep the last N turns in context, and previous ones in a vector store. When the user asks something, search for relevant turns from the history. More complex but preserves detail.
  - **Structured memory**: extract facts from the conversation (e.g.: "the user is a frontend developer", "they already understand the auth architecture") and store them in a database. Include the relevant facts in each prompt. More robust but requires good extraction.
- Implement the chosen strategy and integrate it with the assistant
- Repeat the baseline tests: same long conversation, measure the same metrics
- Compare: does the version with memory maintain coherence for more turns? At what cost in tokens/latency? Does it lose important information?

**One well-implemented and compared strategy is enough for the MVP.** Implementing a second one is an extension.

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again. Did AI help design the memory strategy or did the team design it alone?

---

## 6. Extension

- Implement a **second memory strategy** and compare the three versions (baseline, strategy 1, strategy 2) with the same metrics
- Add **persistent memory across sessions**: have the assistant remember previous conversations when the user returns days later
- Implement **intelligent forgetting**: have the assistant discard information that's no longer relevant (e.g.: "the user has already gotten past the auth onboarding, that information can be archived")
- Add **meta-memory**: have the assistant be able to answer "what do you know about me?" and list what it remembers about the user
- Experiment with **large context windows** (models with 128k+ tokens) and compare: does a huge window make the memory strategy unnecessary, or is there still degradation?

---

## 7. Suggested stack

| Component | Tool |
|---|---|
| Assistant | Python + conversational model (OpenAI, Ollama, etc.) |
| CLI | Python + Typer or Rich for conversational interface |
| Vector store (if you choose retrieval) | Chroma or FAISS (local, no server) |
| Structured memory (if you go that route) | SQLite |
| Metrics | Token counting (tiktoken), timestamps for latency |

No cloud infrastructure required. Everything runs locally.

---

## 8. Terrain to explore

- How many interactions can an LLM with context stuffing endure before degrading? Does it depend on the model, the type of conversation, or both?
- How do you measure "coherence" in a long conversation? Is it subjective or can it be automated (e.g.: ask the model about information from previous turns)?
- What does each memory strategy lose? Does summarization lose detail? Does retrieval lose temporal order? Does structured memory lose nuance?
- How much context is "enough" for each message? Is there an optimal point between too little (the model has no information) and too much (the model gets confused with so much context)?
- Does the memory strategy change the assistant's personality or tone? Does a summary distort the relationship with the user?
- What happens when the memory contains incorrect or outdated information? How do you correct it?
- Did the assistant AI help design the memory strategy or was it necessary to research and design manually?
- Do models with long context windows (128k+) make these strategies unnecessary, or do they simply postpone the problem?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — MemoryArch — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Windsurf | Building the assistant and the memory strategy | |
| Chroma / FAISS (if applicable) | Vector store for retrieval-based memory | |

### Biggest AI impact in this lab
[Did AI help more in building the baseline assistant, designing the memory
strategy, implementing the vector store/summarization, or analyzing the
comparative metrics? Did AI suggest a strategy the team hadn't
considered?]

### Moment where AI didn't help or introduced problems
[Did AI suggest a memory strategy too complex for the available time?
Did the assistant with memory lose important information that the baseline
didn't lose? Did AI not understand the trade-offs between strategies?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Spend time experimenting with the baseline limits before implementing
memory — seeing exactly when and how it degrades gives you the intuition to choose
the right strategy. Summarization is the most accessible to start with."]
```
