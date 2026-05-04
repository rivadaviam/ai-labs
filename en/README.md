# AI Labs — Practical AI Program for Development Teams

> 🇪🇸 [Versión en español](../README.md)

Hands-on labs for teams of 2–3 people. The goal is learning — building something is the means. Each lab is anchored in a current AI pattern and covers both **using AI to build** and **learning applied GenAI patterns** on real development problems.

The program runs on **AWS as the reference infrastructure** — participants have access to a corporate account. Labs 00–06 use native AWS services (Bedrock, Lambda, CDK, CloudWatch, IAM, S3); labs 07–18 are cloud-agnostic and can run in any environment.

📋 **Program criteria and philosophy:** [CRITERIA.md](CRITERIA.md)

---

## Lab Format

| Parameter | Value |
|---|---|
| Teams | 2–3 people |
| Duration per lab | 3 weeks |
| Commitment | ~4 hours/week/person (24–36 hours total) |
| Closing | Informal show & tell of the process, what was built, and possible improvements |
| AI Tools | Codex, Windsurf, Copilot (corporate) + Claude (personal) |
| Infrastructure | AWS (corporate account) — cost < $5–10 per team per lab |

Each lab has 9 sections: name, skill, problem, product, MVP, extension, stack, terrain to explore, and AI reflection.

---

## Available Labs

| # | Lab | Skill | Artifact | Infra |
|---|-----|-------|----------|-------|
| 00 | [AI Functions — Your First Agentic Prototype](lab-00-AI-functions.md) | AI Functions + Agentic flows | Script / notebook | AWS ☁️ |
| 01 | [RAG Health Monitor](lab-01-rag-debugging.md) | RAG pipeline observability | Lambda + CloudWatch dashboard | AWS ☁️ |
| 02 | [Guardrails Calibration Tool](lab-02-guardrails-tracing.md) | Guardrails evaluation (FP/FN) | CLI + HTML report | AWS ☁️ |
| 03 | [AI Governance Scanner](lab-03-guardrails-iam.md) | AI governance + IAM compliance | CLI audit + ready-to-apply fixes | AWS ☁️ |
| 04 | [Live KB Sync Pipeline](lab-04-kb-sync-eventdriven.md) | Event-driven + Knowledge Bases | CDK stack + test harness | AWS ☁️ |
| 05 | [Semantic Search Benchmark](lab-05-vector-search-s3.md) | Vector search benchmarking | Script + comparative report | AWS ☁️ |
| 06 | [Resilient LLM Gateway](lab-06-cross-region-inference.md) | Resilience + cross-region inference | FastAPI proxy + metrics | AWS ☁️ |
| 07 | [CodeReviewer AI](lab-07-code-reviewer-ai.md) | LLM workflows + structured output | GitHub Action | — |
| 08 | [PromptBench](lab-08-prompt-bench.md) | Prompt evaluation / LLM-as-judge | CLI + HTML report | — |
| 09 | [AgentOps](lab-09-agentops.md) | AI Agents + tool use (ReAct) | Interactive CLI | — |
| 10 | [DocSense](lab-10-docsense.md) | Structured extraction + docs | CLI + REST API | — |
| 11 | [KnowledgeBot](lab-11-knowledge-bot.md) | RAG with evaluation and feedback loop | Slack bot / CLI | — |
| 12 | [SpecFirst — Spec-Driven Development](lab-12-spec-driven.md) | Spec-driven development | Spec + feature + adherence report | — |
| 13 | [ContextBridge — MCP: Use and Build](lab-13-mcp.md) | Model Context Protocol (MCP) | Custom MCP server + integration demo | — |
| 14 | [SkillForge — Build and Use Agent Skills](lab-14-agent-skills.md) | Agent Skills | 2-3 working skills + demo | — |
| 15 | [MultiAgent — Multi-Agent Orchestration](lab-15-multi-agent.md) | Multi-agent orchestration | CLI + consolidated report | — |
| 16 | [RedTeam — AI Security and Red Teaming](lab-16-red-team.md) | AI Security / Red teaming | Target chatbot + CLI redteam + report | — |
| 17 | [VisualQA — Applied Multimodal AI for Development](lab-17-multimodal.md) | Multimodal AI (vision) | CLI + visual regression report | — |
| 18 | [MemoryArch — Context and Memory Management](lab-18-memory.md) | Context and memory management | Assistant with memory + metrics | — |

---

## How to Use These Labs

Each lab includes:
- **Problem** — Realistic development team scenario
- **Target product** — What can be built + suggested validations to test it
- **MVP (3 weeks)** — What can be achieved at ~4 hrs/week/person, with reflection checkpoints at the close of each phase
- **Stack** — Only prescribed if the technology is part of the learning; otherwise the team chooses freely
- **Terrain to explore** — Key concepts, expected phenomena, and questions the lab should answer — including questions about AI use during the build
- **AI Reflection** — Mandatory synthesis of the reflection checkpoints collected throughout the lab

Labs are **independent**: any team can do any lab in any order.

---

## Suggested Learning Paths

| I want to learn... | Suggested labs |
|---|---|
| My first steps with AI on AWS | 00, 01 |
| End-to-end AWS AI stack | 00 → 01 → 04 → 06 |
| How to monitor and debug AI in production | 01, 02 |
| How to govern AI use in the organization | 03, 02 |
| How to keep AI documentation up to date | 04, 11 |
| How to choose and compare vector search services | 05 |
| How to make an app resilient to throttling | 06 |
| How to automate code review with AI | 07 |
| How to systematically evaluate and improve prompts | 08 |
| How to build a diagnostic agent | 09 |
| How to extract data from unstructured documents | 10 |
| How to build an internal documentation bot | 11 |
| How to develop guided by AI-generated specifications | 12 |
| How to extend an AI agent with custom tools (MCP) | 13 |
| How to package team knowledge for AI agents | 14 |
| How to coordinate multiple AI agents to solve a complex problem | 15 |
| How to test AI system security before launch | 16 |
| How to use vision models to automate UI visual review | 17 |
| How to manage long conversations without losing context | 18 |

---

## Required Level

Labs require:
- Python and basic terminal skills
- Cloud concepts (S3, serverless functions) for AWS labs
- Curiosity and willingness to experiment — no prior LLM experience required

No ML experience or vector math background needed.
