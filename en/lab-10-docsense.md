# Lab 10: DocSense — Intelligent Extractor for Technical Documents

> 🇪🇸 [Versión en español](../lab-10-docsense.md)

**Team:** 2–3 people
**Level:** Intermediate

---

## 1. Lab name

**DocSense** — Pipeline for structured information extraction from unstructured documents with a configurable schema

---

## 2. Main skill

**Structured Outputs + Document Understanding** — Using LLMs as intelligent parsers of complex documents with forced output schemas (JSON Schema / Pydantic) and per-field confidence scoring.

---

## 3. Problem to solve

The DevOps or architecture team receives between 20 and 80 pages of technical specifications (RFCs, architecture decision records, vendor contracts, runbooks, change requests) in PDF. Manually extracting critical data — dependency versions, SLAs, endpoints, mandatory configurations, deprecation dates, escalation contacts — takes 2-3 hours per document. With high volume, important data gets lost. Worse: different people extract the same fields inconsistently.

---

## 4. Target product

**A CLI + REST API** that receives a PDF or a directory of PDFs, extracts structured entities according to a configurable schema, and returns clean JSON with a confidence field for each extracted value.

**Suggested validations:**

- Run extraction on a test document and verify the output JSON respects the defined schema
- Verify the confidence scores reflect the real difficulty of extraction (ambiguous fields should have low scores)
- Change the YAML schema for another type of document and verify it works without changing code
- Generate the HTML report and verify low-confidence fields are visually highlighted
- Test with at least 2 documents of different complexity to evaluate robustness

*In the show & tell, the team shows the validations they ran and centers the conversation on decisions made, problems encountered, and lessons learned.*

---

## 5. MVP scope

**First phase — Basic extraction with configurable schema:**
- CLI with an `extract` command that accepts a PDF or directory + schema file
- Configurable YAML schema: the user defines field name, type (string, date, number, list), description in natural language, and whether it's required
- PDF processing: text extraction with PyMuPDF, chunking by sections preserving titles as context
- Call the LLM with output forced to the JSON Schema

> **Reflection checkpoint:** Note which AI tools you used in this phase, on which tasks, and a moment where AI didn't help or caused problems.

**Second phase — Confidence scores, HTML report, and API:**
- Confidence score per field: the LLM produces a justification and a 0.0–1.0 score along with the value
- Output: structured JSON + HTML report with fields highlighted by confidence level
- REST API with FastAPI: `POST /extract` endpoint that accepts PDF as multipart/form-data and returns the JSON

> **Reflection checkpoint:** Note whether AI changed how you approached this phase vs. the first, and what you would adjust about AI use if starting again.

---

## 6. Extension

- Batch mode: process a folder of 50+ documents, consolidated output in CSV/parquet for analysis
- Automatic document type detection (RFC, contract, runbook) and automatic selection of a predefined schema
- Validation and ground-truth system: load manual annotations and measure field-level accuracy of the extractor
- Web interface (Streamlit or Gradio) to upload documents, view real-time extraction with source-text highlighting
- Integration with Notion or Confluence API to publish extractions directly into documentation pages

---

## 7. Suggested stack

**Free / open-source:**
- Ollama + Mistral 7B or Llama3 with grammar sampling for forced JSON
- PyMuPDF (fitz) for PDF text extraction
- Pydantic v2 for schema validation and JSON Schema generation
- Typer for CLI
- FastAPI for the REST API
- Jinja2 for HTML report

**Cloud alternatives:**
- OpenAI GPT-4o with `response_format: { type: "json_schema" }` (very reliable for extraction)
- Anthropic Claude with the `tool_use` pattern for structured extraction
- Google Gemini 1.5 Flash (1M-token context window, ideal for long documents, cheap)

---

## 8. Terrain to explore

- Why is text extraction from PDFs with multi-column tables hard? What information gets lost and how does it affect the LLM?
- How do you calibrate a confidence score in document extraction? What does it mean if the model reports 0.90 for everything?
- What happens when a field appears multiple times with different values in the same document? What are the resolution strategies and their trade-offs?
- When should you force structured output with grammar sampling vs. trust the prompt and few-shot examples?
- How do you design an extraction schema that's portable across different document types without changing code?
- What's the relationship between document size, the model's context window, and processing latency?
- What makes a document hard to extract even for a large model?
- Did AI help design the YAML schema and few-shot examples, or did the team have to iterate manually until extraction was reliable?
- In which part of the lab did AI save the most time: PDF parsing, extraction prompt design, or REST API construction?

---

## 9. AI Reflection

Synthesis of the reflection checkpoints collected during the lab. Presented as part of the show & tell.

```
## AI Reflection — DocSense — [Team]

### Tools used
| Tool | What we used it for | Result (1-5) |
|---|---|---|
| Codex / Copilot | Implementing PDF parsing and the REST API | |
| Claude / ChatGPT | Designing the YAML schema and few-shot examples for the extraction prompt | |
| Windsurf / Cursor | Building the CLI and the HTML report generator | |

### Biggest AI impact in this lab
[Did AI help more in designing the YAML extraction schema, writing the few-shot
examples for the prompt, implementing PDF parsing, or generating the HTML report?]

### Moment where AI didn't help or introduced problems
[Did the LLM ignore the schema on documents with complex formatting? Were the confidence
scores always 0.9 regardless of difficulty? Did PDF parsing with tables
lose critical information?]

### Change in the development cycle
Did AI change *how* the team worked (not just speed it up)? Describe a concrete example
of a decision you made differently because you had AI available, or explain why
the workflow didn't change.

### Recommendation for the next team
[e.g.: "Invest time in real few-shot examples before testing on hard documents
— it makes a difference. Parsing PDFs with tables is the biggest technical obstacle:
try PyMuPDF and pdfplumber and pick the one that best preserves structure."]
```
