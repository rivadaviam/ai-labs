# Lab 14: SkillForge — Build and Use Agent Skills

> 🇪🇸 [Versión en español](../lab-14-agent-skills.md)

**Team:** 2–3 people
**Level:** Introductory–Intermediate

---

## 1. Lab name

**SkillForge: Build and Use Agent Skills** — Install existing skills from the ecosystem, understand the SKILL.md format, and create custom skills that package reusable development workflows

---

## 2. Main skill

**Agent Skills** — Open format ([agentskills.io](https://agentskills.io/home)) for extending AI agent capabilities with domain knowledge and reusable workflows. A skill is a directory with a `SKILL.md` (frontmatter with name/description + instructions in the body), optionally accompanied by `scripts/`, `references/`, and `assets/`. Agents discover skills by their metadata, activate them when the task matches the description, and execute the instructions.

---

## 3. Problem to solve

The development team has workflows it repeats constantly: setting up a new microservice with the team's structure, doing code reviews following internal conventions, debugging performance problems with a specific checklist, or preparing a PR for merge. Each developer does it slightly differently, and when they use an AI agent in the IDE, the agent doesn't know the team's conventions — it generates generic code, ignores internal patterns, and doesn't follow the checklists the team has built up over time.

The question is: **how do you package that tacit team knowledge in a format that an AI agent can discover, activate, and execute automatically?**

The lab proposes a concrete domain to work in: **code review with team conventions**. The team defines its review conventions (naming, error handling, testing, documentation), packages them as Agent Skills, and validates that the agent applies them when reviewing code. Teams with another domain idea can choose freely (microservice setup, debugging workflows, PR preparation, etc.).

---

## 4. Target product

A **set of 2-3 functional Agent Skills** that package the team's development workflows, with evidence that a compatible agent discovers, activates, and executes them correctly.

**Suggested validations:**

- Install at least 2 existing skills from the ecosystem and verify the agent discovers and activates them with the correct prompt
- Read the `SKILL.md` of the installed skills and document: what makes the description good or bad, how the instructions are structured, what patterns they use
- Create at least 2 custom skills following the spec and verify the agent discovers them (they appear when listing skills)
- Test each custom skill with at least 3 different prompts: one that should activate it, one that's ambiguous, and one that shouldn't activate it — document whether the agent gets it right
- Compare the agent's output with and without the skills installed for the same task — did the skill improve the result quality?

*In the show & tell, the team shows the created skills, the activation demo, and centers the conversation on the design process for the instructions and the decisions made.*

---

## 5. MVP scope

**First phase — Understand the format: install and explore existing skills:**
- Environment setup: Windsurf, or VS Code + GitHub Copilot in Agent mode (both support Agent Skills)
- Install 2-3 skills from the existing ecosystem in `.agents/skills/` — recommended sources:
  - [Skills curated by OpenAI](https://github.com/openai/skills/tree/main/skills/.curated): `security-best-practices`, `doc`, `playwright`, `gh-fix-ci`
  - [skills.sh directory](https://skills.sh/): `systematic-debugging`, `test-driven-development`, `code-review`
- Test each installed skill: activate it with a prompt, observe how the agent loads the instructions, verify it executes what's indicated
- Read and analyze the `SKILL.md` of the installed skills: study the structure (frontmatter, body, scripts, references), identify which instruction patterns work well

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and what you observed about how the agent discovers and activates skills. Did any skill fail to activate when it should have? Did any activate when it shouldn't have?

**Second phase — Create custom skills:**
- Define the work domain: code review with team conventions (or the domain chosen by the team)
- Design 2-3 skills for that domain. Example for code review:
  - `review-naming`: reviews team naming conventions (prefixes, suffixes, casing)
  - `review-error-handling`: verifies error handling patterns according to the team's standard
  - `review-checklist`: runs a complete pre-merge review checklist
- Implement each skill: create the directory, write the `SKILL.md` with frontmatter and body, add `scripts/` or `references/` if appropriate
- Test activation and quality: verify the agent discovers them, activates them with the correct prompt, and produces useful output
- Iterate on the descriptions and instructions until activation is reliable

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again. Did you use AI to write the skills? Did it work?

---

## 6. Extension

- Add **executable scripts** to the skills: have the code review skill run a custom linter, or have the debugging skill run a profiler automatically
- Implement **progressive disclosure**: skills with `references/` that the agent loads only when more detail is needed (e.g.: full conventions guide vs. quick summary)
- Create a **meta-level skill**: a skill that helps create other skills (a team "skill creator")
- Evaluate skill quality using the [agentskills.io evaluation framework](https://agentskills.io/skill-creation/evaluating-skills): measure correct activation rate, output quality, and false positives
- Publish the team's skills in a shared repository so other teams can install and use them

---

## 7. Suggested stack

| Component | Tool |
|---|---|
| IDE | VS Code + Codex or Windsurf |
| Skills format | Agent Skills spec ([agentskills.io/specification](https://agentskills.io/specification)) |
| Validation | `skills-ref validate ./my-skill` ([skills-ref library](https://github.com/agentskills/agentskills/tree/main/skills-ref)) |
| Scripting language | Bash or Python (for skills with executable scripts) |
| Skill catalogs | [OpenAI curated](https://github.com/openai/skills/tree/main/skills/.curated), [skills.sh](https://skills.sh/) |

No cloud infrastructure or external services required. Everything runs locally in the IDE.

---

## 8. Terrain to explore

- What makes a skill description good? How much detail does it need so the agent activates it correctly without over-activating it?
- How do you write a `SKILL.md` that's useful for the agent *and* readable for a human who wants to audit or improve it?
- What kind of knowledge packages well into a skill (procedures, checklists, conventions) and what kind doesn't (intuition, judgment, implicit context)?
- How many skills can an agent handle before it starts to get confused or pick the wrong skill?
- How do you iterate on a skill? What feedback loop works — testing with varied prompts, reading the agent trace, comparing output with/without skill?
- Are skills portable across agents (Windsurf, Copilot)? Do they behave the same or are there differences in how each agent interprets them?
- What happens when two skills cover overlapping domains? Does the agent choose the right one to activate?
- Did the assistant AI help write the skill instructions, or did the team have to iterate manually until the instructions were specific and actionable?
- Is it harder to write a good description (so the agent discovers it) or good instructions (so the agent executes them well)?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — SkillForge — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Windsurf | Testing installed skills and the ones we created | |
| Codex | Generating drafts of SKILL.md and scripts | |
| skills-ref | Validating the format of the created skills | |

### Biggest AI impact in this lab
[Did AI help more in writing the skill instructions, designing the descriptions
for correct activation, generating the scripts, or somewhere else?
Did you use AI to create skills about how to use AI?]

### Moment where AI didn't help or introduced problems
[Did AI generate descriptions too generic that activated with any prompt?
Were the AI-generated instructions too vague to be actionable?
Did the agent fail to discover the skills or confuse them with each other?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Start by installing and reading existing skills before creating yours — understanding
what makes a skill work well is easier seeing it than reading the spec. The
description is the most critical component: if it isn't right, the skill never activates."]
```
