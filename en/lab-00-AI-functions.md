# Lab 00: AI Functions — Your First Agentic Prototype

> 🇪🇸 [Versión en español](../lab-00-AI-functions.md)

**Team:** 2–3 people
**Level:** Introductory — entry point to the program

---

## 1. Lab name

**AI Functions: Your First Agentic Prototype** — Build an agentic pipeline with functions described in natural language, post-conditions for self-correction, and flows that combine multiple functions

---

## 2. Main skill

**AI Functions + Structured Outputs + Agentic Flows** — Designing functions executed by an AI model that return structured Python objects, with post-conditions that validate the output and trigger self-correction cycles when needed.

---

## 3. Problem to solve

The team picks **one** of these three scenarios (or proposes their own):

**Scenario A — Log Intelligence**
The team has server logs (real anonymized or synthetic). Processing them manually to detect patterns, classify errors by severity, and identify root causes takes hours. Nobody does it systematically and recurring problems keep happening.

**Scenario B — Meeting Intelligence**
The team has meeting transcripts. Manually extracting action items, owners, decisions made, and unresolved topics takes 20-30 minutes per meeting and the result is inconsistent across people.

**Scenario C — Code Quality Reporter**
The team has a Python module or service nobody wants to document or review because it's tedious. Generating docstrings, detecting code smells, and producing a quality report manually takes hours and always gets postponed.

**Or your own scenario:** A repetitive process in the team's work that involves transforming unstructured text or data into structured, actionable output.

---

## 4. Target product

A **Python script or notebook** that implements an agentic pipeline with:
- At least **2 AI Functions** defined for the chosen scenario
- **Post-conditions** in each function that validate the output and trigger self-correction when they fail
- A **combined flow** that chains the functions (sequential or asynchronous)
- Visible evidence that a post-condition caught at least one incorrect output and the model self-corrected it

**Suggested validations:**

- Run the pipeline with at least 3 different inputs and verify the output matches the expected schema in all cases
- Force a post-condition to fail (use an input that produces incomplete output) and verify the model self-corrects successfully
- Compare the pipeline output with what the team would have produced manually for the same input
- Review the self-correction log: verify it shows where the post-condition failed, what prompt was used for the retry, and how the output changed between attempts

*In the show & tell, the team shows the validations they ran and centers the conversation on decisions made, problems encountered, and lessons learned.*

---

## 5. MVP scope

**First phase — First AI Function working:**
- Environment setup: Python 3.12+, install `strands-ai-functions`, model access (Codex, GPT-4o-mini, or Amazon Bedrock)
- Define and run **one** AI Function for the chosen scenario
- Explore the repository examples (`meeting_summary.py`, `universal_loader.py`, `stock_report.py`) to understand patterns
- Implement a simple post-condition that validates the output and manually retry at least once

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems.

**Second phase — Complete pipeline:**
- Second AI Function that uses the output of the first
- Post-conditions in both functions with automatic (not manual) self-correction
- Script or notebook that runs the pipeline end-to-end
- Test dataset prepared (minimum 3-5 different inputs for the demo)
- Documented evidence of self-corrections: at least 1 case where the post-condition caught a bad output and the model corrected it

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

---

## 6. Extension

- Implement the pipeline with **async functions**: have two AI Functions run in parallel when they don't depend on each other, and measure the impact on total time
- Add a **third function** that consolidates previous outputs into a final report (PDF, markdown, or structured JSON)
- Experiment with **different models**: run the same pipeline with GPT-4o-mini, Claude Haiku, and Llama3 via Ollama. Compare output quality and post-condition activation rate
- Measure and visualize **self-correction stats**: post-condition failure rate, number of retries per function, additional time per self-correction

---

## 7. Suggested stack

**Main option:**

| Component | Tool |
|---|---|
| AI Functions | `strands-ai-functions` (see repository README) |
| Model | GitHub Copilot Codex / GPT-4o-mini via API |
| Python | 3.12+ |
| Environment | venv or conda |

**With AWS (corporate option):**

| Component | Service |
|---|---|
| Model | Amazon Bedrock — Claude Haiku or Titan (low cost) |
| AI Functions | `strands-ai-functions` with backend configured for Bedrock |
| Access | Corporate AWS account — cost < $1 for the entire lab |

**Local alternative ($0 cost):**

| Component | Tool |
|---|---|
| Model | Ollama + `llama3.1` or `mistral` (local, no API cost) |
| AI Functions | `strands-ai-functions` with backend configured for Ollama |

---

## 8. Terrain to explore

- What happens when a post-condition is too strict? And when it's too loose? How do you find the balance between both extremes?
- How many self-correction attempts does the model need before giving up or getting it right? Are there cases where it never converges?
- How does model choice (local vs. API, size) affect output schema consistency?
- What makes a two-AI-Function pipeline harder to debug than a single one?
- How do you design a post-condition that is verifiable and specific without being brittle to minor output variations?
- When is it better to start with the simplest possible case vs. going straight to the full scenario?
- What kind of test inputs trigger post-conditions? What inputs never trigger them even when the output is bad?
- How is using AI to write AI Functions different from using it to write conventional code? What changes in the workflow?
- Was the AI you used to build the pipeline able to design good post-conditions, or did it tend to produce trivial validations that never fail?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — AI Functions — [Team] — [Chosen scenario]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Copilot | Writing the pipeline code and post-conditions | |
| Claude / ChatGPT | Designing the AI Functions prompts | |
| Windsurf / Cursor | Exploring the repository and understanding examples | |

### Biggest AI impact in this lab
[Did AI help more in writing the AI Functions, designing the post-conditions,
debugging the retries, or somewhere else? Did the AI you used to build the lab also
use AI Functions to do it?]

### Moment where AI didn't help or introduced problems
[Did the model chosen for the AI Functions fail to respect the output schema? Did the AI
that assisted you generate post-conditions that never triggered? Was there an infinite
retry loop that wouldn't end?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Start with the simplest possible scenario and a single test input until
the first retry works. Scale up only when you have evidence of real self-correction,
not before."]
```
