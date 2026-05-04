# AI Labs Program Criteria

> Source of truth for the program. Updated as it evolves.
> Last updated: 2026-04-30

> 🇪🇸 [Versión en español](../CRITERIA.md)

---

## Program Philosophy

The labs don't teach theory or prepare for certifications. They are designed for small teams to learn by **doing**: the goal is learning, not the product. Building something is the means — the process of making decisions, hitting obstacles, and solving them with AI is what matters.

There are two simultaneous learning axes:

1. **Building with AI** — learning applied GenAI patterns (RAG, agents, evals, etc.) by building a functional product
2. **Using AI to build** — developing good AI usage habits in the development cycle, and documenting how those practices improve over time

The second axis is as important as the first. Each lab is an opportunity to observe how AI changes the workflow and feed those practices back into the rest of the program.

---

## Program Parameters

| Parameter | Value |
|---|---|
| **Team size** | 2–3 people |
| **Duration per lab** | 3 weeks |
| **Weekly commitment** | ~4 hours per week per person |
| **Estimated total effort** | 24–36 hours per team per lab |
| **Labs** | Independent (any order; different teams can run different labs in parallel) |
| **Participant level** | Mixed (do not assume prior LLM experience) |
| **Lab closing** | Informal show & tell of the learning process, what was built, and possible improvements |

---

## Available AI Tools

Teams choose the tools they prefer. Available corporate tools:
- **Codex** — code generation
- **Windsurf** — IDE with integrated agent
- **GitHub Copilot** — inline completion in IDE

Additional tools participants may incorporate on their own:
- **Claude** (Claude.ai or Claude Code) — recommended for design tasks, complex debugging, and code generation with broad context

**No tool is mandatory.** Part of the learning is choosing, testing, and evaluating which tools work best for each task. This is documented in the improvement loop.

---

## Infrastructure

- **AWS is the program's reference environment** — participants have access to a corporate account and labs 00–06 are designed and validated on native AWS services (Bedrock, Lambda, CDK, CloudWatch, IAM, S3 Vectors)
- **Responsible use** — AWS cost per lab should be minimal (ideally < $5–10 per team)
- Prioritize services with a free tier or usage-based pricing over fixed-cost resources (e.g. S3 Vectors over an OpenSearch cluster)
- Labs 07–18 are cloud-agnostic and can run without an AWS account
- Teams without access to specific AWS services may use open-source/local alternatives (Ollama, Chroma, FAISS), but must document how their experience differs from the AWS reference path

---

## Learning Objectives

### 1. Current and Relevant GenAI Patterns
Each lab is anchored in a pattern that development teams need to handle today:

| Pattern | Labs that cover it |
|---|---|
| RAG with quality evaluation | Lab 11 |
| AI Agents with tool use (ReAct) | Lab 09 |
| Prompt evaluation / LLM-as-judge | Lab 08 |
| Structured outputs and document extraction | Lab 10 |
| LLM workflows and structured output | Lab 07 |
| Guardrails and governance | Labs 02, 03 |
| Embeddings and vector search | Labs 01, 05 |
| Event-driven with Knowledge Bases | Lab 04 |
| Cross-region inference and throttling | Lab 06 |
| Spec-driven development | Lab 12 |
| Model Context Protocol (MCP) | Lab 13 |
| Agent Skills | Lab 14 |
| Multi-agent orchestration | Lab 15 |
| AI Security / Red teaming | Lab 16 |
| Multimodal AI (vision) | Lab 17 |
| Context and memory management | Lab 18 |

### 2. Real GenAI Problems
Lab scenarios must reflect problems teams face in production:
- Low-quality or irrelevant responses in RAG
- Agents that loop or hallucinate tool results
- Prompts that silently degrade
- Documents with information extracted inconsistently
- Guardrails that generate false positives and block legitimate queries

### 3. Good AI Practices in the Development Cycle
Participants practice and document AI use across different phases:

| Cycle phase | Practices to explore |
|---|---|
| **Design** | Generate architecture options, evaluate trade-offs with AI |
| **Code** | Inline completion, boilerplate generation, assisted refactoring |
| **Testing** | Generate test cases, write tests from natural language descriptions |
| **Debugging** | Diagnose errors, interpret stack traces, propose fixes |
| **Documentation** | Generate docstrings, READMEs, ADRs from code |
| **Code review** | Detect issues, suggest improvements before human review |

---

## The Improvement Loop

The closing of each lab is an **informal show & tell**: the team shares the learning process, what was built, and possible improvements or continuations.

The **AI Reflection** is built **during** the lab, not at the end. Each lab includes **reflection checkpoints** at the close of each MVP phase, where the team briefly notes which AI tools they used, on which tasks, and what they observed. Section 9 of each lab is the **synthesis** of those checkpoints — presented as an addendum to the show & tell.

The reflection template (included in each lab) structures the final synthesis:

```
## AI Reflection — [Lab Name] — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| [tool] | [specific task] | [1-5] |

### Biggest AI impact in this lab
[2-3 sentence description: which task was significantly accelerated or improved]

### Moment where AI didn't help or introduced problems
[2-3 sentence description: what went wrong, hallucinations, insufficient context, etc.]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example of a decision made differently because AI was available, or explain why the flow didn't change.

### Recommendation for the next team doing this lab
[1-2 sentences: what advice would you give about how to use AI in this specific lab]
```

---

## Criteria for a Good Lab

### Design Criteria

- [ ] **Clear target product** — something concrete and demonstrable is built, not just code read or questions answered
- [ ] **Achievable in 24–36 team hours** — the MVP is completable in 3 weeks at ~4 hrs/week/person
- [ ] **Anchored in a real AI pattern** — teaches a current pattern, not theory or certification prep
- [ ] **Realistic problem** — the scenario is credible for software, DevOps, or technical teams
- [ ] **Requires technical decisions** — the team makes design decisions, not just follows instructions
- [ ] **Comparable across teams** — different teams can compare their results and learnings
- [ ] **Stack in service of the concept** — a specific technology is only prescribed if learning it is part of the lab's objective; otherwise the team chooses freely
- [ ] **AWS as the default environment** — if the lab involves cloud infrastructure, it must be designed and documented on AWS; using a different stack requires explicit justification
- [ ] **Minimal AWS cost** — resources created should not exceed ~$5–10 per team for the MVP

### Lab Closing Criteria

- [ ] **Show & tell completed** — the team presented their learning process, what was built (if applicable), and possible continuations
- [ ] **Technical decisions justified** — the team can explain the choices they made and what they learned from them
- [ ] **AI Reflection completed** — the team presents the synthesis of reflection checkpoints collected during the lab

### Anti-criteria

- [ ] Do not base learning objectives on certification questions
- [ ] Do not be a code walkthrough with no product to build
- [ ] Do not depend on sensitive or production data
- [ ] Do not require expensive or fixed-cost infrastructure for the MVP
- [ ] Do not ignore the "AI assists the team while doing the lab" axis — reflection checkpoints exist for this
- [ ] Do not assume all participants have the same level

---

## Standard Lab Structure

Each lab has these **9 sections**:

| # | Section | Description |
|---|---|---|
| 1 | **Name** | Clear and engaging |
| 2 | **Primary skill** | AI pattern being explored |
| 3 | **Problem to solve** | Concrete and realistic scenario |
| 4 | **Target product** | What is built + suggested validations to test it |
| 5 | **MVP scope** | What can be achieved in 3 weeks at 4 hrs/week/person, with reflection checkpoints at the close of each phase |
| 6 | **Extension** | How it evolves if the team decides to continue |
| 7 | **Suggested stack** | Only mentioned if the technology is part of the learning; otherwise the team chooses |
| 8 | **Terrain to explore** | Key concepts, expected phenomena, and questions the lab should answer — including questions about AI use during the build |
| 9 | **AI Reflection** | Mandatory synthesis of the reflection checkpoints collected during the lab |

---

## Scope of Current Labs

| Lab | Name | Primary Skill | AWS |
|---|---|---|---|
| 00 | AI Functions | AI Functions + Agentic flows | ☁️ Bedrock |
| 01 | RAG Health Monitor | RAG pipeline observability | ☁️ Lambda, CloudWatch, Bedrock |
| 02 | Guardrails Calibration Tool | Guardrails evaluation (FP/FN) | ☁️ Bedrock Guardrails |
| 03 | AI Governance Scanner | AI governance + IAM compliance | ☁️ IAM, Bedrock |
| 04 | Live KB Sync Pipeline | Event-driven + Knowledge Bases | ☁️ CDK, S3, Bedrock KB |
| 05 | Semantic Search Benchmark | Vector search benchmarking | ☁️ S3 Vectors, Bedrock |
| 06 | Resilient LLM Gateway | Resilience + cross-region inference | ☁️ Bedrock cross-region |
| 07 | CodeReviewer AI | LLM workflows + structured output | — |
| 08 | PromptBench | Prompt evaluation / LLM-as-judge | — |
| 09 | AgentOps | AI Agents + tool use (ReAct) | — |
| 10 | DocSense | Structured extraction + docs | — |
| 11 | KnowledgeBot | RAG with evaluation and feedback loop | — |
| 12 | SpecFirst | Spec-driven development | — |
| 13 | ContextBridge | Model Context Protocol (MCP) | — |
| 14 | SkillForge | Agent Skills | — |
| 15 | MultiAgent | Multi-agent orchestration | — |
| 16 | RedTeam | AI Security / Red teaming | — |
| 17 | VisualQA | Multimodal AI (vision) | — |
| 18 | MemoryArch | Context and memory management | — |

---

## Changelog

| Date | Change |
|---|---|
| 2026-03-30 | Initial creation. Labs 01-06 identified as derived from AWS certification. Labs 07-11 created as product labs. |
| 2026-03-30 | Criteria refined: 2-3 week duration / 4 hrs/week, tools Codex+Windsurf+Copilot+Claude, independent labs, mixed level, low-cost corporate AWS, improvement loop = demo + document. |
| 2026-03-31 | Philosophy refocused: learning is the goal, building is the means. Fixed 3-week duration. Deliverable → informal show & tell. AI Reflection optional. "Evaluation criteria" + "Risks" sections merged into "Terrain to explore". Prescriptive stack only if technology is the learning. AWS preferred but not required. Format distinction between labs removed. |
| 2026-04-02 | Labs 12-14 added: SpecFirst (spec-driven development), ContextBridge (MCP: use and build), SkillForge (Agent Skills). Reinforce axis 2 ("using AI to build"). |
| 2026-04-02 | Labs 15-18 added as candidates: MultiAgent (multi-agent orchestration), RedTeam (security/red teaming), VisualQA (multimodal AI), MemoryArch (context and memory management). AITrace (agent usage analysis) discarded: Cascade data stored in proprietary protobuf with no documented schema. Pending team review. |
| 2026-04-30 | AWS promoted to program reference environment. Labs 00–06 marked as AWS-native with specific services. "Preferred but not required" criterion replaced with "default environment with explicit justification required for deviations". AWS column added to scope table. English version created at en/. |
