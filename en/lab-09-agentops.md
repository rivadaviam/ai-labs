# Lab 09: AgentOps — Autonomous Infrastructure Diagnosis Agent

> 🇪🇸 [Versión en español](../lab-09-agentops.md)

**Team:** 2–3 people
**Level:** Intermediate–Advanced

---

## 1. Lab name

**AgentOps** — AI agent with tools to diagnose infrastructure problems from natural language

---

## 2. Main skill

**AI Agents + Tool Use** — Designing agents with the ReAct (Reason + Act) loop, function calling, state handling, and safety limits for autonomous operations.

---

## 3. Problem to solve

When there's a production incident at 2am, the on-call engineer needs to run 10-15 commands across different systems (logs, metrics, health checks, DB queries) before understanding what's happening. This process is slow, error-prone under pressure, and the knowledge about which commands to run is scattered across outdated runbooks. There's no way to say "the `/api/users` endpoint responds in 8 seconds" and have the system reason about what to check and in what order.

---

## 4. Target product

**An interactive CLI** that accepts a problem description in natural language, autonomously runs a sequence of diagnostics using tools, and produces a probable root cause report with evidence.

**Suggested validations:**

- Run a diagnostic with a known problem and verify the agent reaches the correct root cause
- Verify the reasoning trace is readable and clearly shows why the agent chose each tool
- Test with an ambiguous problem and verify the agent doesn't hallucinate tool results or enter a loop
- Verify the step limit (maximum 10 tool calls) works correctly
- Test with at least 2 different diagnosis scenarios to verify generality

*In the show & tell, the team shows the validations they ran and centers the conversation on decisions made, problems encountered, and lessons learned.*

---

## 5. MVP scope

**First phase — Tools and basic ReAct loop:**
- CLI with a `diagnose` command that accepts a problem description in natural language
- Set of 6–8 tools implemented with realistic simulated data: `get_recent_logs(service, minutes)`, `get_db_query_times(endpoint)`, `get_memory_usage(service)`, `get_active_connections()`, `get_recent_deploys(hours)`, `run_health_check(service)`
- ReAct loop implemented manually or with LangChain/LangGraph: the agent reasons, picks a tool, runs it, observes the result, reasons again

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems.

**Second phase — Observability and robustness:**
- Visible reasoning in the terminal in real time (streaming the agent's "thought" before each tool call)
- Hard step limit: maximum 10 tool calls per diagnosis to avoid infinite loops
- Structured final report: probable cause, evidence (which tool/result supports it), confidence level, recommended next steps

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

---

## 6. Extension

- Connect real tools: queries to PostgreSQL, calls to Prometheus/Grafana API, parsing logs from files or CloudWatch
- "Automatic runbook" mode: if the agent identifies the problem with confidence > 0.85, it can propose and (with explicit user confirmation) execute the remediation
- Diagnostic history in SQLite: previous diagnoses inform reasoning ("this kind of problem was solved 3 times before like this")
- Web interface with reasoning graph visualization (nodes = tool calls, edges = reasoning steps)
- Integration with PagerDuty or OpsGenie to start a diagnosis automatically from an alert

---

## 7. Suggested stack

**Free / open-source:**
- Ollama + Llama 3.1 8B (supports native function calling from llama3.1)
- Manual implementation of the ReAct loop in Python (recommended for learning the mechanism before using frameworks)
- Rich (Python) for terminal output with colors, tables, and spinners
- SQLite for diagnostic history
- Typer for CLI

**Cloud alternatives:**
- OpenAI GPT-4o mini (excellent tool calling, very cheap for iterative agents)
- Anthropic Claude 3.5 Haiku (very reliable tool use)
- Groq Llama3.1 70B (free, very low latency for agent loops)

---

## 8. Terrain to explore

- How does a loop manifest in a ReAct agent? What patterns in the trace indicate the agent is repeating steps without progressing?
- What makes an LLM hallucinate tool results instead of calling them? How can the system prompt influence this behavior?
- How do you measure agent efficiency? When do more tool calls indicate confusion vs. legitimate problem complexity?
- What information from a tool is necessary in the agent's context vs. what is noise that affects decisions?
- How does agent behavior vary between models? What capabilities are critical for reliable function calling?
- What makes an agent's reasoning readable in the trace? When is it opaque and why?
- When are tools with simulated data sufficient to validate the agent and when aren't they?
- Did the assistant AI help design the agent's system prompt, or did the team have to iterate on it manually until the behavior was useful?
- Did the AI you used to build the agent run into the same problems (loops, tool hallucinations) as the agent you're building?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — AgentOps — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Copilot | Implementing the simulated tools and ReAct loop | |
| Claude / ChatGPT | Designing the agent's system prompt and generating simulated data | |
| Windsurf / Cursor | Debugging agent behavior and refactoring | |

### Biggest AI impact in this lab
[Did AI help more in designing the agent's system prompt, generating realistic simulated
data for the tools, implementing the ReAct loop, or debugging agent loops?]

### Moment where AI didn't help or introduced problems
[Did AI suggest a tool architecture too complex for the MVP? Did the agent
you built hallucinate tool results? Did the AI-generated system prompt produce
opaque reasoning or loops?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Start with the manual ReAct loop before using frameworks — AI can help you
understand the mechanism step by step. The system prompt is the most critical component:
invest time iterating on it before adding more tools."]
```
