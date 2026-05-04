# Lab 15: MultiAgent — Multi-Agent Orchestration for Code Review

> 🇪🇸 [Versión en español](../lab-15-multi-agent.md)

**Team:** 2–3 people
**Level:** Intermediate–Advanced

---

## 1. Lab name

**MultiAgent: Multi-Agent Orchestration** — Build a system where specialized agents collaborate, coordinated by a supervisor, to solve a task with multiple dimensions

---

## 2. Main skill

**Multi-agent orchestration** — Designing systems with multiple specialized AI agents that communicate with each other, coordinated by a supervisor agent that decides who to invoke, resolves conflicts between findings, and synthesizes results. The team learns when agent specialization adds value, how communication between them is designed, and what coordination patterns exist (sequential, parallel, hierarchical).

---

## 3. Problem to solve

A development team wants to automate Pull Request reviews, but the problem has multiple dimensions a single prompt doesn't cover well: code quality (naming, complexity, duplication), security (vulnerable dependencies, exposed secrets, SQL injection), and test coverage (functions without tests, uncovered edge cases). A single generalist agent tends to give superficial feedback across all dimensions or to focus on only one and forget the others.

The central question of the lab is: **how do you design a system where specialized agents analyze different aspects of the same problem and a supervisor synthesizes their findings into a coherent result?**

The lab proposes PR review as the reference scenario. Teams with another idea for a multi-dimensional problem can choose freely — the important thing is that the problem has at least 2 clear dimensions that justify specialized agents.

---

## 4. Target product

A **multi-agent system** (2 specialized agents + 1 supervisor) that analyzes a diff or code directory and produces a consolidated report with findings categorized by dimension.

**Suggested validations:**

- Verify each specialized agent produces findings relevant to its dimension (they don't overlap or repeat)
- Verify the supervisor invokes the correct agents based on the diff content (e.g.: if there are no dependency changes, don't invoke the dependency security agent)
- Test with at least 2 different diffs: one with quality problems and another with security problems — verify the system adapts
- Verify the final report synthesizes without contradicting itself (if one agent says "the naming is correct" and another doesn't mention it, the report shouldn't invent naming problems)
- Test what happens if an agent finds no findings — the supervisor should handle the case without inventing content

*In the show & tell, the team shows the system demo, the generated report, and centers the conversation on design decisions: how they divided responsibilities, how the agents communicate, and what problems they encountered in coordination.*

---

## 5. MVP scope

**First phase — One specialized agent working:**
- Environment setup: Python + model with function calling (via API or local with Ollama)
- Design the first specialized agent (e.g.: code quality) with its system prompt, tools, and output format
- Implement the agent: receives a diff or files, analyzes its dimension, produces structured findings
- Test the agent with 2-3 example diffs and verify the findings are relevant and actionable
- Understand the pattern: an agent = system prompt + tools + output schema. This pattern is replicated in phase 2.

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems. Does the specialized agent produce more useful feedback than a generic prompt?

**Second phase — Second agent + supervisor:**
- Implement the second specialized agent (e.g.: security) with the same pattern as phase 1
- Design and implement the supervisor: decides which agents to invoke, passes them the relevant context, receives their findings, resolves conflicts or redundancies, and synthesizes a unified report
- Implement the full flow: input (diff) → supervisor → agents → supervisor → report
- Test the full system with diffs covering both dimensions and verify the report is coherent

**2 agents + 1 supervisor is the complete MVP product**, not the minimum. If there's time, adding a third agent is an extension.

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again. Was it harder to design the agents or the supervisor?

---

## 6. Extension

- Add a **third specialized agent** (e.g.: test coverage) and verify how the supervisor's coordination scales
- Implement **parallel execution** of agents (instead of sequential) and compare latency
- Add **agent-to-agent communication**: have one agent ask another a question (e.g.: the test agent asks the quality agent which functions are most complex to prioritize them)
- Implement **debate between agents**: when faced with an ambiguous finding, two agents argue for and against, and the supervisor decides
- Connect the system to **GitHub** so it analyzes real PRs from the team
- Compare the output of the multi-agent system with the output of a single generalist agent with a long prompt — when does each approach win?

---

## 7. Suggested stack

| Component | Tool |
|---|---|
| Orchestration | LangGraph (recommended) or manual Python implementation |
| Model | Any with function calling (GPT-4o mini, Llama 3.1 via Ollama, etc.) |
| CLI | Python + Typer |
| Output | Markdown or HTML report |
| Test data | Code diffs (can be from open-source repos or the team's own) |

No cloud infrastructure required. Everything runs locally.

---

## 8. Terrain to explore

- How do you decide which dimensions deserve a specialized agent and which can be covered with a broader prompt? Where's the line between useful specialization and unnecessary complexity?
- How do you design the system prompt of a specialized agent so it stays in its domain and doesn't invade others'? What happens when domains overlap?
- What role does the supervisor play? Is it a router, a synthesizer, an arbiter, or all three? How does the supervisor's complexity change as you add more agents?
- How do agents communicate? Free text, structured JSON, or a defined protocol? Which format produces better results?
- What happens when two agents contradict each other? Can the supervisor resolve the conflict or does it need an explicit rule?
- How much context does each agent need? Is it better to pass the full diff or only the portion relevant to its specialty?
- Is multi-agent slower and more expensive than a single agent? When is the overhead justified?
- Did the assistant AI help design the agents' system prompts, or did the team have to iterate manually until each agent stayed in its domain?
- Is building a multi-agent system more like designing an organization of people or designing a microservices architecture? Which metaphor helps more?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — MultiAgent — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Windsurf | Implementing the agents and the supervisor | |
| LangGraph / manual | Orchestrating the multi-agent flow | |

### Biggest AI impact in this lab
[Did AI help more in designing the agents' system prompts, implementing
the supervisor, defining the inter-agent communication format, or debugging
the flow when agents contradicted or repeated themselves?]

### Moment where AI didn't help or introduced problems
[Did AI generate system prompts that made agents invade each other's domains?
Did the AI-generated supervisor synthesize findings poorly?
Did LangGraph have a different API from what the AI knew?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Start with a single agent and make it work well before adding the second.
The supervisor is the hardest component — don't underestimate how much design it needs
to synthesize findings without inventing."]
```
