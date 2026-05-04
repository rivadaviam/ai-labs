# Lab 13: ContextBridge — MCP: Use and Build Context Servers

> 🇪🇸 [Versión en español](../lab-13-mcp.md)

**Team:** 2–3 people
**Level:** Intermediate–Advanced

---

## 1. Lab name

**ContextBridge: Use and Build MCP Servers** — Integrate existing MCP servers to power an AI agent, and build your own MCP server that exposes product-specific operations

---

## 2. Main skill

**Model Context Protocol (MCP)** — Understand, use, and build MCP servers that expose tools and data to AI agents in the IDE. The team learns how agents discover and use external tools, what makes an MCP server useful, and when it's worth building one vs. using existing ones.

---

## 3. Problem to solve

A team needs to build an internal tool — for example, an incident management system where developers register, search, and close incidents with their resolution history. The lab guides the team to build this tool **using existing MCPs during the development process**: configuring filesystem, SQLite, and GitHub MCPs in their IDE so the AI agent has direct access to the code, the database, and the repository while they build.

Once the tool is working, a natural problem arises: the AI agent that helped them build it cannot *use* it as a user. It can't query open incidents, can't search the resolution history, can't update an incident's status — because there's no MCP server for that tool. Generic MCPs (filesystem, SQLite) allow access to files and the database in raw form, but don't expose the product's domain operations.

The question is: **how do you build an MCP server that gives the agent intelligent access to a product the team just created?**

The lab proposes building an incident management system as the reference product. Teams with another product idea can choose freely — the important thing is that the product has domain operations that justify a custom MCP.

---

## 4. Target product

A **functional tool** (the incident system or the chosen product) built with the assistance of existing MCPs, plus a **custom MCP server** that exposes that tool's domain operations, demonstrating the agent can interact with the product intelligently.

**Suggested validations:**

- Configure at least 2 third-party MCPs in the IDE and verify the agent uses them during product development (e.g.: querying the database, reading project files)
- Build the functional tool with at least 3-4 domain operations (e.g.: create, list, search, close incidents)
- Build the custom MCP server and verify the agent discovers it and can invoke its tools
- Solve a concrete task that requires the agent to combine third-party MCPs with the custom MCP (e.g.: "what critical incidents are open and which relate to the latest deploy?")
- Verify the MCP server handles errors correctly (invalid parameters, missing data)

*In the show & tell, the team shows the built product, the MCP server, the demo of the agent using them, and centers the conversation on design decisions and problems encountered.*

---

## 5. MVP scope

**First phase — Build the product using existing MCPs:**
- Environment setup: configure the IDE (Windsurf or VS Code + Copilot) to use MCP servers
- Integrate 2-3 third-party MCPs: filesystem (access to project files), SQLite (direct queries to the database), and optionally GitHub (for the repository)
- Build the incident management system (or chosen product) with the agent assisted by MCPs: REST API with 3-4 endpoints + SQLite + sample data
- Observe how MCPs change the development experience: does the agent query the database directly? does it read project files without being pasted? is it more useful than without MCPs?
- Document: which tasks the generic MCPs solved well during development, and which domain operations of the just-built product aren't covered

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and how third-party MCPs changed (or didn't) the development experience.

**Second phase — Build an MCP server for the product:**
- Design the MCP server's tools: which domain operations to expose, which parameters they accept, what they return (based on the gap identified in phase 1)
- Implement the MCP server using the official SDK (TypeScript or Python)
- Define at least 3 tools: a read one (e.g.: `get_critical_incidents`), a search one (e.g.: `search_resolution_history`), and an action one (e.g.: `update_incident_status`)
- Connect the MCP server to the IDE and verify the agent discovers and uses it
- Integration demo: solve a task that combines third-party MCPs + the custom MCP (e.g.: "list open critical incidents and correlate them with the latest commits in the repo")

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

---

## 6. Extension

- Add **authentication and permissions** to the MCP server: different users see different incidents based on their role
- Implement **more complex write tools**: let the agent create incidents, assign owners, or close incidents with an automatically generated summary
- Add **resources** to the MCP server (in addition to tools): expose static data such as system documentation, runbooks, or database schemas
- Publish the MCP server as an **installable package** (npm or pip) so other teams can use it
- Connect the MCP server to a **real system** of the team: a production API, a monitoring service, or a ticketing system

---

## 7. Suggested stack

**Product (incident system or similar):**

| Component | Tool |
|---|---|
| API | FastAPI (Python) or Express (Node.js) |
| Database | SQLite |
| Language | Python (recommended) or whatever the team prefers |

**Suggested third-party MCPs:**

| MCP | Used for |
|---|---|
| filesystem | Read project files (code, configs, docs) |
| sqlite | Direct SQL queries to the system's database |
| github | Correlate incidents with PRs, commits, and deploys |

**IDE:**
- Windsurf (native MCP support) or VS Code + compatible extension

---

## 8. Terrain to explore

- What makes an MCP tool "discoverable" by the agent? How do the name, description, and parameter schema affect whether the agent picks it correctly?
- When is a generic MCP (e.g.: SQLite) enough and when do you need a specific one? Where's the line between "I can run a SQL query" and "I need a domain tool"?
- What happens when the agent has many tools available? Does it choose the right one or get confused?
- How do you design MCP tools that return useful information without flooding the agent's context?
- What errors does the agent make when using tools? Does it pass wrong parameters? Does it invent tools that don't exist?
- How do you test an MCP server without depending on the agent? Can you validate the interface independently?
- How hard is it to go from an MCP server prototype to something another team can install and use?
- Did the assistant AI help design the MCP's tools, or did the team have to iterate on the descriptions manually until the agent used them correctly?
- Is building an MCP server more like designing a REST API or designing a prompt? Which skills apply and which are new?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — ContextBridge — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Windsurf | Building the product and the MCP server with MCP assistance | |
| Third-party MCPs | Powering the agent during development (filesystem, SQLite, GitHub) | |

### Biggest AI impact in this lab
[Did AI help more in building the product with MCPs, designing the custom MCP's tools,
implementing the server, or debugging the integration with the agent?
Did third-party MCPs change the development experience significantly?]

### Moment where AI didn't help or introduced problems
[Did AI generate tool descriptions the agent didn't understand? Did the agent confuse
similar tools? Did the SDK have breaking changes the AI didn't know about?
Did the agent hallucinate tools that didn't exist?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Start with the third-party MCPs to understand how the agent discovers and uses
tools. Don't jump straight to building your own MCP — phase 1 gives you the intuition
you need to design good tool descriptions."]
```
