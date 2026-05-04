# Lab 07: CodeReviewer AI — The Reviewer Who Never Sleeps

> 🇪🇸 [Versión en español](../lab-07-code-reviewer-ai.md)

**Team:** 2–3 people
**Level:** Intermediate

---

## 1. Lab name

**CodeReviewer AI** — LLM-assisted code review pipeline as a GitHub Action

---

## 2. Main skill

**LLM Workflows + Structured Outputs** — Designing multi-prompt pipelines that produce structured (JSON) output, with parsing and action on the results.

---

## 3. Problem to solve

The team receives PRs late in the day. Human reviewers are busy or in another timezone. Review comments are inconsistent: sometimes superficial, sometimes excessive. There's no automated first pass that detects problematic patterns (hardcoded secrets, missing error handling, SQL injection) before a human reviewer arrives — which means reviewers spend time on things a tool could catch.

---

## 4. Target product

**A GitHub Action** that, when a PR is opened or updated, analyzes the diff with an LLM and posts automatic comments directly on specific code lines inside the PR on GitHub.

**Suggested validations:**

- Open a PR with at least 3 planted bugs (e.g.: hardcoded secret, unsanitized SQL query, empty try/catch) and verify the bot posts comments on the correct lines
- Verify each comment includes category, explanation, and fix suggestion in structured format
- Open a clean PR (no bugs) and verify the bot doesn't generate excessive false positives
- Test with a large diff (>200 lines) and verify per-file processing works correctly

*In the show & tell, the team shows the validations they ran and centers the conversation on decisions made, problems encountered, and lessons learned.*

---

## 5. MVP scope

**First phase — Basic review pipeline:**
- Working GitHub Action that triggers on `pull_request` events (opened, synchronize)
- Extracts the PR diff using the GitHub REST API
- Builds a prompt with the diff + context (repo language, change purpose)
- Calls an LLM and forces structured JSON output: `[{file, line, severity, category, message, suggestion}]`

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems.

**Second phase — Publication and robustness:**
- Parses the JSON and posts comments on specific diff lines via the GitHub Review API
- Minimum categories: `security`, `error-handling`, `readability`
- Safety limit: if the diff exceeds N lines, process file-by-file individually

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

---

## 6. Extension

- Feedback system: developers mark comments as "useful" / "false positive" via GitHub emoji reactions
- Accumulated feedback feeds an evaluation dataset to measure reviewer precision
- Second LLM as a meta-evaluator that measures the quality of the first one's comments
- Streamlit dashboard showing: precision@k, most frequent categories, false positive rate by issue type
- `.ai-review.yml` file in the repo allowing custom rules and categories to ignore

---

## 7. Suggested stack

**Free / open-source:**
- Ollama + Codestral 7B or llama3.1 (zero cost, runs locally or on EC2 free tier)
- GitHub Actions (free for public repos or with free-tier minutes)
- Python + PyGithub + httpx
- Streamlit for metrics dashboard

**Cloud alternatives:**
- OpenAI GPT-4o mini (very cheap for medium diffs, ~$0.001/PR)
- Anthropic Claude Haiku
- Groq (very low latency, generous free tier)

---

## 8. Terrain to explore

- What happens when the diff exceeds the model's context window? How does the strategy change depending on whether the limit is hit on a file vs. on the full PR?
- Why does the LLM hallucinate line numbers and what information in the prompt reduces this behavior?
- How many prompt iterations are typically needed for the comments to be useful? What criteria define a "useful" comment vs. noise?
- How do you measure the quality of an automatic code review? What makes a comment actionable?
- How does the chosen model (local vs. API, size) affect structured JSON output consistency?
- What categories of issues does an LLM detect well, and which does it detect poorly compared to a human reviewer?
- What happens when many comments are posted at the same time on the GitHub API?
- Does the AI you used to build the code reviewer produce comments similar to or different from the code reviewer you built? What does that say about the task?
- How many prompt iterations did the team need before the LLM stopped hallucinating line numbers? Did the assistant AI help solve that problem?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — CodeReviewer AI — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Copilot | Writing the GitHub Action and diff parsing | |
| Claude / ChatGPT | Designing the review prompt and iterating on comment quality | |
| Windsurf / Cursor | Integrating with the GitHub Review API and debugging the Action | |

### Biggest AI impact in this lab
[Did AI help more in generating the review prompt, parsing the diff, writing the
Action's tests, or solving the line number hallucination?]

### Moment where AI didn't help or introduced problems
[Did AI hallucinate line numbers in the JSON output? Did it generate code incompatible
with the GitHub Review API? Did the review prompt produce generic comments without value?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Use AI to generate the first review prompt, but expect to iterate on it 2-3 times
before the results are good. Line number hallucination is the most common
problem and requires specific information in the prompt to solve."]
```
