# Lab 12: SpecFirst — Spec-Driven Development

> 🇪🇸 [Versión en español](../lab-12-spec-driven.md)

**Team:** 2–3 people
**Level:** Intermediate

---

## 1. Lab name

**SpecFirst: Spec-driven development** — Generate specification documents with AI and use them as the source of truth to build a feature end-to-end

---

## 2. Main skill

**Spec-Driven Development** — Use AI tools to generate structured specification documents (PRD, user stories, acceptance criteria) and then use those documents as anchors so AI generates code, tests, and validations aligned with the requirements. The team evaluates how much of the generated output adheres to what was specified and where it diverges.

---

## 3. Problem to solve

A team receives a vague request: "we need an internal tool that lets developers register and search the team's technical decisions (ADRs — Architecture Decision Records)". There's no PRD, no stories, no acceptance criteria. Just an idea and an informal conversation.

When the team jumps straight into code, the result is unpredictable: each developer interprets the request differently, scope grows out of control, and in the end no one knows whether what was built solves what was asked. The question is: **what happens if before writing a line of code, the team generates a formal specification with AI and then uses that specification to guide the entire build?**

---

## 4. Target product

A **functional ADR management tool** (CLI, web app, or API) built entirely from an AI-generated specification, along with an **adherence report** that documents how much of the generated code aligned with the spec and where it diverged.

**Suggested validations:**

- Verify each user story in the spec has at least one functionality implemented in the code
- Run the spec's acceptance criteria as tests (manual or automated) and document which pass and which fail
- Identify at least 2 cases where AI-generated code diverged from the spec and document why
- Compare the effort of building with a spec vs. the team's previous experience building without one

*In the show & tell, the team shows the generated spec, the built product, and the adherence report. The conversation centers on the spec→code process and the decisions made.*

---

## 5. MVP scope

**First phase — Generate the specification:**
- Install and configure the spec generation tool (BMAD is one option we've seen in show-and-tells, but spec-kit or another relevant tool can be used)
- From the lab guidelines (product description, constraints, target users), generate: PRD with scope and goals, 4-6 user stories with acceptance criteria, and a proposed technical architecture
- Review and refine the generated spec — the team makes decisions about what to include in the MVP and what to discard
- Document the refinement decisions: what changed between the original spec and the final version

> **Reflection checkpoint:** Note which AI tools you used to generate the spec, how many iterations were needed, and a moment where AI generated something that didn't make sense or was too generic.

**Second phase — Build from the specification:**
- Use the spec as explicit context for the AI agent in the IDE (Windsurf, Copilot): include the spec in the project as a reference document
- Implement the prioritized user stories: the team builds the ADR tool guided by the spec
- Generate tests from the spec's acceptance criteria (the agent should be able to derive tests directly from the criteria)
- Produce the adherence report: for each user story, document whether it was implemented, whether the code aligns with the spec, and where it diverged

> **Reflection checkpoint:** Note whether having the spec changed how the team interacted with AI when generating code. Did AI produce output more aligned with the goal? Or did it ignore the spec and generate what it wanted?

---

## 6. Extension

- Experiment with **different levels of detail in the spec**: generate a minimal spec (just PRD) and a detailed spec (PRD + stories + criteria + architecture) and compare the quality of generated code in each case
- Implement an **automatic validation pipeline**: a script that parses the spec and automatically verifies which acceptance criteria are met in the code
- Try **different models or agents** building from the same spec and compare adherence — does the model matter more or does the spec matter more?
- Iterate the spec after phase 2: use findings from the adherence report to refine the spec and rebuild — measure if the second iteration improves

---

## 7. Suggested stack

**Spec generation:**

| Component | Tool |
|---|---|
| Spec generation | BMAD (main option) — framework to generate PRDs, stories, and specs from guidelines |
| Alternative | Structured prompt + Windsurf/Copilot — a well-designed PRD template as a prompt |
| Spec format | Markdown (versionable, readable, portable) |

**Building the product:**

| Component | Tool |
|---|---|
| IDE with agent | Windsurf or VS Code + Copilot |
| Language | Python (recommended) or whatever the team prefers |
| Storage | SQLite or markdown files (for the ADRs) |
| CLI | Typer or argparse |
| Web (if you choose web) | FastAPI + htmx or Streamlit |

---

## 8. Terrain to explore

- What level of detail in the spec produces better AI-generated code? Is there a point where more detail stops helping or even confuses the model?
- How do you design a spec that's useful for humans *and* for AI agents? Are they the same specs or do they need different formats?
- Does AI tend to ignore parts of the spec? Which? Are there patterns in what the model "forgets" or reinterprets?
- What happens when the spec has ambiguities? Does the model resolve them reasonably or amplify them?
- Do the spec's acceptance criteria serve as the basis for automated tests, or do they require significant rewording?
- How much effort goes into generating and refining the spec vs. how much is saved during the build phase? Is the balance positive?
- Does BMAD (or your chosen alternative) produce specs the team would use in a real project, or are they too generic?
- Did the team's interaction with AI change once they had a reference spec? Were the prompts different? Was the output more predictable?
- What decisions did the team make that they wouldn't have made without the spec? Did the spec function as a useful constraint or as an obstacle?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — SpecFirst — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| BMAD / alternative | Generating the initial specification | |
| Codex / Windsurf | Building the product from the spec | |
| Codex / Windsurf | Generating tests from acceptance criteria | |

### Biggest AI impact in this lab
[Did AI help more in generating the spec, building code from the spec, generating
tests from the acceptance criteria, or somewhere else? Did having a spec change the
quality of AI's output?]

### Moment where AI didn't help or introduced problems
[Was the generated spec too generic and had to be rewritten? Did AI ignore parts
of the spec when generating code? Did the tests generated from the spec fail to cover
the real cases?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Invest time refining the spec before starting to code. The first version that
BMAD generates is a starting point, not a finished product. AI-generated code is
as good as the spec you give it."]
```
