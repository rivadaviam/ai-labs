# Lab 17: VisualQA — Applied Multimodal AI for Development

> 🇪🇸 [Versión en español](../lab-17-multimodal.md)

**Team:** 2–3 people
**Level:** Intermediate

---

## 1. Lab name

**VisualQA: Applied Multimodal AI for Development** — Use vision models to automate visual review of interfaces, detect UI regressions, and extract information from images

---

## 2. Main skill

**Multimodal AI (vision)** — Use models with vision capabilities to analyze images in development contexts: compare UI screenshots between versions, describe what the model sees in an interface, detect visual differences, and extract structured data from images (mockups, wireframes, diagrams). The team learns what vision models can and can't do in practical development tasks.

---

## 3. Problem to solve

The team maintains a web application and each release requires visual review: verifying that code changes didn't break the UI on pages that weren't touched. QA does this review manually — they open each page, compare with the previous version, and report differences. This process is slow, error-prone due to visual fatigue, and doesn't scale when the application has dozens of pages.

Pixel-based visual diff tools (like Percy or BackstopJS) detect any change — including irrelevant differences like a different timestamp or a changed avatar. What the team needs is a review that **semantically** understands what changed: "the submit button moved 20px to the right" is different from "the form lost a required field".

The lab guides the team to build a tool that uses vision models for semantic visual review: not just detecting that something changed, but describing what changed and whether it appears intentional or a regression.

---

## 4. Target product

A **CLI** that accepts two sets of screenshots (baseline and current), analyzes them with a vision model, and produces a visual differences report with semantic descriptions of the detected changes.

**Suggested validations:**

- Capture screenshots of a web application in two different versions (can be any public site or your own project)
- Send pairs of screenshots (baseline + current) to the vision model and verify it describes the changes correctly
- Classify the detected changes: regression (probable bug), intentional change, irrelevant difference (dynamic data)
- Generate a report with categorized changes, annotated screenshots, and description of each difference
- Test with at least one case where there are NO changes and verify the model doesn't invent differences

*In the show & tell, the team shows the tool, the generated report, and centers the conversation on the quality of the vision model's descriptions and the false positives/negatives encountered.*

---

## 5. MVP scope

**First phase — Capture and analysis pipeline:**
- Environment setup: Playwright (or Selenium) to capture screenshots automatically from a web application
- Configure access to a model with vision (GPT-4o via API, Llama 3.2 Vision via Ollama, or similar)
- Build the basic pipeline: capture screenshots of N pages → send each (baseline, current) pair to the vision model → ask it to describe the differences
- Experiment with prompts: which instructions produce the best descriptions? Does the model detect subtle changes? Does it invent differences that don't exist?
- Generate a structured output for each pair: page, detected changes, description, estimated severity

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems. Was the vision model more or less accurate than expected?

**Second phase — Report and classification:**
- Implement change classification: regression (the model detects something that looks like a bug), intentional change (the model detects something that looks like a redesign), irrelevant (dynamic data, timestamps)
- Generate an HTML report with: executive summary, list of changes per page, side-by-side screenshots, description of each change, and severity
- Add data extraction from images: given a screenshot of a form, extract the fields and their labels in structured format (JSON)
- Test with at least 5 screenshot pairs and evaluate description quality

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again. Is prompt engineering for vision models different from text models?

---

## 6. Extension

- Integrate with **CI/CD**: capture screenshots automatically on each PR and generate the visual regression report as a PR comment
- Add **visual accessibility analysis**: have the model evaluate contrast, text size, and interactive elements according to WCAG
- Implement **mockup analysis**: given a Figma mockup (exported as image) and a screenshot of the implementation, have the model describe the differences between design and implementation
- Add **change history**: store reports in SQLite to see the visual evolution of the application over time
- Experiment with **local vision models** (Llama 3.2 Vision, LLaVA) vs. cloud models (GPT-4o) and compare description quality

---

## 7. Suggested stack

| Component | Tool |
|---|---|
| Screenshot capture | Playwright (recommended) or Selenium |
| Vision model | GPT-4o (via API), Llama 3.2 Vision (via Ollama), or similar |
| CLI | Python + Typer |
| Report | Jinja2 + HTML |
| Test data | Any public web application or your own project |

No cloud infrastructure required. Everything runs locally (with Ollama for the vision model, or with an OpenAI API key for GPT-4o).

---

## 8. Terrain to explore

- How good are current vision models at detecting differences between two screenshots? Do they detect subtle changes (1px offset, a slightly different color)?
- How do you write an effective prompt for a vision model? Is it different from text prompt engineering? Which instructions improve description accuracy?
- When does the model invent differences that don't exist (visual hallucinations)? Are there patterns that trigger false positives?
- What kind of changes does the model describe well (layout, colors, text) and which does it describe poorly (subtle spacing, animations, hover states)?
- Does a local model (Llama 3.2 Vision) produce descriptions comparable to GPT-4o for this task, or is there a significant quality difference?
- How do you evaluate the quality of a visual description? Do you need a human or can it be automated with a second model?
- Is semantic visual review more useful than pixel diff for the team, or are they complementary?
- Did the assistant AI help design the prompts for the vision model, or did the team have to iterate manually? Does the AI you used to build the tool understand well how vision models work?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — VisualQA — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Windsurf | Building the pipeline and the CLI | |
| Vision model | Analyzing screenshots and describing differences | |
| Playwright | Capturing screenshots automatically | |

### Biggest AI impact in this lab
[Did AI help more in building the pipeline, designing the prompts for the vision
model, generating the report, or another task? Did the vision model exceed
the team's expectations or fall short?]

### Moment where AI didn't help or introduced problems
[Did the vision model invent differences that didn't exist? Did it not detect changes
that were obvious to a human? Did the assistant AI not understand well how to use
the model's vision API?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Start with screenshot pairs where differences are obvious to calibrate
how good the model is. Then try with subtle differences. The prompt
matters a lot — invest time in iterating it."]
```
