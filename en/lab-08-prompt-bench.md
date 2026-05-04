# Lab 08: PromptBench — Systematic Evaluation of Production Prompts

> 🇪🇸 [Versión en español](../lab-08-prompt-bench.md)

**Team:** 2–3 people
**Level:** Intermediate

---

## 1. Lab name

**PromptBench** — Framework for prompt evaluation and comparison with real datasets

---

## 2. Main skill

**Prompt Evaluation + LLM-as-Judge** — Building rigorous evaluation pipelines, automatic metrics, and systematic comparison of prompt variants using an LLM as evaluator.

---

## 3. Problem to solve

The team has an AI feature in production (support ticket classifier, response generator, form data extractor) and wants to improve the prompt. Each change is validated "by eye" or by showing it to someone on the team to see if "it looks better." There is no rigorous process: no evaluation dataset, no metrics, and no way to know whether a change improved the general case or just the example used to iterate. Prompt changes go to production without a safety net.

---

## 4. Target product

**A CLI + HTML report** that, given a dataset of (input, expected_output) pairs and two versions of a prompt, runs an automatic evaluation and produces a comparative report with: statistical winner, detected regression cases, and improvements by category.

**Suggested validations:**

- Run `compare` with two versions of a prompt and verify the report correctly identifies which is better and in which cases
- Verify the detected regressions are real: manually review the inputs the system marks as "losses"
- Compare the results of the 3 evaluation modes (exact match, LLM-as-judge, similarity) on the same dataset and analyze discrepancies
- Run the same benchmark twice and verify the reproducibility of the results

*In the show & tell, the team shows the validations they ran and centers the conversation on decisions made, problems encountered, and lessons learned.*

---

## 5. MVP scope

**First phase — Runner and basic evaluation:**
- CLI with a `compare` command that accepts two prompts (`.txt` files) + dataset + evaluation model
- Dataset format: JSONL with `input`, `expected`, `category` (optional) fields
- Runner: runs both prompts in parallel against all dataset examples with controlled concurrency
- At least 1 evaluation mode working (exact match or LLM-as-judge)

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems.

**Second phase — HTML report and full evaluation modes:**
- 3 selectable evaluation modes: exact match, LLM-as-judge, similarity score (embeddings cosine)
- Output: JSON of results + HTML report with wins/losses table, regression examples, and per-category breakdown
- Additional `dataset expand` command to auto-generate more test cases from a few examples (few-shot dataset expansion)

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

---

## 6. Extension

- A/B testing mode in production: split traffic between prompt_v1 and prompt_v2 with automatic logging, to evaluate with real data
- Web dashboard (Streamlit) with evaluation history and metric evolution over time (each evaluation run is saved)
- CI/CD integration: GitHub Action that blocks the deploy if the new prompt has a regression greater than 5% vs. the current prompt
- Reusable prebuilt evaluators: coherence, tone consistency, factual accuracy, toxicity
- Support for multi-turn prompt evaluation (chat systems, not just single-turn)

---

## 7. Suggested stack

**Free / open-source:**
- Ollama + Llama3 as LLM-as-judge (local, zero cost)
- sentence-transformers (`all-MiniLM-L6-v2`) for similarity evaluation (100% local)
- asyncio + httpx for parallel calls to the LLM
- Pandas + Jinja2 for HTML report
- Typer for CLI

**Cloud alternatives:**
- OpenAI GPT-4o mini as judge (very reliable, ~$0.002 per evaluation of 100 examples)
- Anthropic Claude Haiku
- Weights & Biases (free tier) for experiment logging and dashboard

---

## 8. Terrain to explore

- What are the known biases of an LLM-as-judge? How does response elaboration vs. correctness affect the score it assigns?
- How do you calibrate an LLM-judge? What does it mean for it to agree with human evaluation 80% of the time? What does that say about the remaining 20%?
- What happens when exact match and LLM-judge give contradictory results? How do you decide which to believe?
- What makes a prompt evaluation dataset representative? How does case distribution affect the final metrics?
- How do you manage concurrency in LLM API calls without exceeding rate limits?
- What information does an evaluation report need so a PM can make a decision about which prompt to use in production?
- When does result reproducibility fail? What factors introduce variation between two runs of the same benchmark?
- Was AI useful for generating the evaluation dataset, or were the generated cases not very diverse and biased toward the happy path?
- Was the LLM-as-judge prompt designed by the team or generated by AI? How did that decision affect evaluation quality?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — PromptBench — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Copilot | Implementing the asyncio runner and result parsing | |
| Claude / ChatGPT | Designing the LLM-judge prompt and generating test cases | |
| Windsurf / Cursor | Building the CLI and HTML report generator | |

### Biggest AI impact in this lab
[Did AI help more in generating the test dataset, writing the LLM-judge prompt,
implementing the asyncio runner, or designing the HTML report?]

### Moment where AI didn't help or introduced problems
[Was the AI-generated LLM-judge too lenient and approved incorrect responses?
Were the generated test cases too similar to each other? Did the concurrency code
have race conditions?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Use AI to generate the first batch of test cases but review them manually —
they tend not to be very diverse. The LLM-judge prompt is critical: invest time
calibrating it against human evaluation before trusting the results."]
```
