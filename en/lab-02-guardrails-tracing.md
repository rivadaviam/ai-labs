# Lab 02: Guardrails Calibration Tool — Systematic Measurement of False Positives

> 🇪🇸 [Versión en español](../lab-02-guardrails-tracing.md)

**Team:** 2–3 people
**Level:** Intermediate

---

## 1. Lab name

**Guardrails Calibration Tool** — Systematic measurement of false positives and negatives in Bedrock Guardrails

---

## 2. Main skill

**Evaluation and calibration of AI moderation systems** — Designing test suites, analyzing confusion matrices, and iteratively optimizing guardrail policies.

---

## 3. Problem to solve

AI guardrails are configured once and rarely revisited. In production, this creates two problems that coexist:

- **False positives:** legitimate user queries blocked because the guardrail detects a superficial pattern (the word "account" triggering the `BANK_ACCOUNT_NUMBER` policy)
- **False negatives:** problematic content that passes through without being blocked because the guardrail doesn't cover variations of the original pattern

Without a measurement tool, there's no way to quantify the real impact. The support team reports "users are complaining," but no one knows if that represents 2% or 30% of legitimate queries.

The team will build `guardrail-bench`: a CLI that turns guardrail calibration from intuition into a measurable, repeatable process.

---

## 4. Target product

A `guardrail-bench` CLI that accepts:
- A JSONL file of test cases with schema `{prompt, category, expected_action, policy_type}`
- A guardrail ID

And produces an **HTML report** with:
- Confusion matrix per policy type (ContentPolicy, SensitiveInformationPolicy, TopicPolicy, PromptAttackPolicy)
- False positive and false negative rate per policy
- Table of the top 10 most problematic cases with prompt, expected action, actual action, and guardrail assessment
- Global accuracy score

**Suggested validations:**

- Run the CLI against the full dataset and verify the HTML report is generated correctly with the per-policy confusion matrices
- Identify at least one real false-positive case in the report and adjust the guardrail configuration to reduce it
- Re-run after the adjustment and verify the FP rate dropped without introducing new false negatives
- Review the table of problematic cases: are the listed prompts actually problematic, or are they legitimate detections?

*In the show & tell, the team shows the validations they ran and centers the conversation on decisions made, problems encountered, and lessons learned.*

---

## 5. MVP scope

**First phase — Dataset and CLI core:**
- Create a 40-case test dataset in `financial_qa.jsonl`, covering all 4 policies
- Implement the runner: read JSONL, call `ApplyGuardrail` for each case, parse the response
- Compute the confusion matrix per policy
- Output to JSON with all results

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems.

**Second phase — HTML report and calibration flow:**
- Jinja2 template for the HTML report with the 4 confusion matrix widgets
- Top 10 problematic cases table with sorting by error type
- `--compare report_v1.json report_v2.json` command that produces an improvement diff between two runs
- Document the CLI options with `--help`

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

---

## 6. Extension

- Support for **multiple guardrails in parallel**: compare the performance of two configurations on the same dataset
- **Automatic generation of adversarial test cases** using Bedrock: given a set of blocked prompts, generate variations that try to evade the guardrail
- `--interactive` mode that lets you review each problematic case and decide whether to adjust the dataset or the guardrail
- Export the report in **CSV format** for analysis in external tools

---

## 7. Suggested stack

**AWS (recommended):**

| Component | Service |
|---|---|
| Guardrails | Amazon Bedrock Guardrails (`ApplyGuardrail` API) |
| CLI | Python 3.12 + Typer |
| HTTP client | boto3 |
| Templating | Jinja2 |
| HTML visualization | Chart.js (via CDN) |

Estimated cost: < $1 per team (each `ApplyGuardrail` call costs ~$0.0015; 40 cases × 5 runs = ~$0.30).

**Non-AWS alternative:**
Replace Bedrock Guardrails with the **OpenAI Moderation API** (free). The input/output schema is different but the CLI logic is identical.

---

## 8. Terrain to explore

- What distinguishes a false positive from a legitimate detection in a content guardrail? How do you design test cases that cover both sides of that line?
- How do you measure whether a guardrail config change improved or worsened the calibration? What does the delta between v1 and v2 say?
- What makes an evaluation dataset representative? What happens if all cases are obvious?
- When does a guardrail that's "perfect" on the test dataset fail in production? What biases does manual case selection introduce?
- How do you present guardrail metrics to a non-technical stakeholder so they can make concrete decisions?
- What behavior differences exist between topic, content, and PII policies? Which are hardest to calibrate and why?
- What happens if the team calibrates a guardrail that's in production? How do you isolate lab work from real impact?
- Was AI capable of generating useful adversarial test cases, or did it tend to produce obvious cases that don't challenge the guardrail?
- How did the team use AI to design the evaluation dataset? What biases did that introduce in the results?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — Guardrails Calibration Tool — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / ChatGPT | Building the Jinja2 template for the HTML report | |
| Claude | Designing ambiguous test cases in the JSONL dataset | |
| Windsurf / Copilot | Generating the CLI runner and response parsing | |

### Biggest AI impact in this lab
[Did AI help more in generating adversarial test cases, in writing the CLI code,
or in structuring the HTML report? Was AI capable of generating prompts that fooled
the guardrail?]

### Moment where AI didn't help or introduced problems
[Did AI generate test cases with the wrong expected_action? Did it suggest code that
called the Bedrock API with deprecated parameters? Was the generated HTML report
visually unusable?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Invest time in the dataset before touching code. A well-thought-out 40-case
dataset is worth more than a perfect CLI with 10 trivial cases."]
```
