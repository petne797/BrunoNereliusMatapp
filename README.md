# AI Project Assistant

> An educational Python project for learning how to build AI agents using Google Gemini.

The project is intentionally built step by step. The goal is not only to create a working AI assistant, but also to learn good software architecture, testing, and AI-agent design.

---

# Vision

The long-term goal is an AI project assistant that can:

- Read and understand project documentation.
- Create and maintain project artifacts.
- Support the entire project lifecycle.
- Keep the human in control.
- Generate high-quality project documentation.

The assistant should help a project manager work faster without taking ownership of project decisions.

---

# Current status

Sprint 1–5 are completed. The supported planning artifacts are **Riskanalys**,
**Tidplan**, **Mål**, **Leveranser**, **WBS**, **Intressentanalys**, and **Kommunikationsplan**. They use the same human-
controlled artifact lifecycle:

```text
Create → review/revise → explicit approval → GeneratedDocument → Office export
```

Implemented capabilities:

- Project document analysis and definition-driven source selection
- Concurrent drafts keyed by artifact type
- Markdown persistence and restore after restart
- Strict canonical Markdown/YAML and transient typed reconstruction for
  structured artifacts
- Versioned DOCX export for Riskanalys, Mål, and Leveranser
- Versioned XLSX export for Tidplan, WBS, Intressentanalys, and Kommunikationsplan
- CLI and a limited local Streamlit demo
- Automated regression coverage plus native Microsoft Word/Excel and human
  visual verification of production Office outputs

Sprint 5 completed WBS discovery, domain design, Excel presentation and the
WBS–Tidplan interaction. Future product ideas remain documented in
`ROADMAP.md` and `IDEAS.md`; this status does not choose the next sprint.

---

# Architecture

The system is intentionally divided into clear responsibilities.

```text
CLI / local Streamlit demo

        │

        ▼

ProjectAssistant

        │

        ▼

Tool Executor

        │

        ▼

Workflow

        │

        ▼

Generator

        │

        ▼

DraftStore

        │

        ▼

GeneratedDocument

        │

        ▼

OfficeDocumentGenerator

    ┌───┴───┐

    ▼       ▼

   DOCX    XLSX
```

Each layer has one primary responsibility.

---

# Important concepts

## ProjectDocument

Represents source material.

Examples:

- Project plan
- Meeting notes
- Risk analysis
- Steering committee documents

These are never modified by the AI.

---

## ArtifactDraft

An editable AI-generated document.

Examples:

- Risk analysis draft
- Communication plan draft
- Project plan draft

The user may revise or approve the draft.

---

## GeneratedDocument

A document that has crossed the approval boundary and is ready for export.

GeneratedDocument is the output boundary of the domain model.

---

# Human in the loop

The assistant never creates final project documentation directly.

The intended lifecycle is:

```text
Create draft

↓

Review

↓

Revise

↓

Approve

↓

Generate document
```

The user always decides when a document becomes approved.

---

# Current project structure

```text
agent/
    workflows/
    generators/
    config/
    document_generate/
    readers/

projects/
    <project>/

tests/

plans/

doc/
```

---

# Running the application

Create a clean virtual environment and install the supported runtime
dependencies from the repository root:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install --upgrade pip
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
```

Run:

```powershell
.\.venv\Scripts\python.exe main.py
```

## Local Streamlit demo

Start the local demo from the project root:

```powershell
.\.venv\Scripts\python.exe -m streamlit run streamlit_app.py
```

The app is a local presentation layer over the existing Project Assistant.
It exposes deterministic, state-correct actions for Riskanalys, Mål,
Leveranser, WBS, Tidplan, and Intressentanalys. Optional create instructions are passed to the
selected artifact workflow, while free dialogue remains a separate route. Set
`GEMINI_API_KEY` in the environment before starting it, because it uses the
same ProjectAssistant bootstrap as the CLI; never place its value in source
code. The CLI remains available. A synthetic, resettable walkthrough is
documented in [`doc/SPRINT6_DEMO.md`](doc/SPRINT6_DEMO.md).

---

# Running tests

Install the supported runtime plus test tooling before running the supported
test entrypoint:

```powershell
.\.venv\Scripts\python.exe -m pip install -r requirements-dev.txt
```

Run the supported network-free regression from the repository root:

```powershell
.\.venv\Scripts\python.exe scripts\run_tests.py supported -q
```

Run focused supported tests with:

```powershell
.\.venv\Scripts\python.exe scripts\run_tests.py focused tests\test_<area>.py -q
```

The repository contains legacy and prototype tests that are intentionally not
part of the supported runtime architecture. See `SPRINTS.md` and the relevant
task plan for the current verification scope.

---

# Development process

Development follows small, reviewable increments. `COLLABORATION.md` is the
authoritative description of roles, model selection, Git review, QA, and task
handoffs.

Typical workflow:

1. Chat and the Product Owner define the task and acceptance criteria.
2. Codex implements and runs automated technical verification on a task branch.
3. Codex pushes a reviewable commit and lightweight GitHub PR.
4. Chat reviews the actual GitHub diff; corrections stay in the same Codex session and PR.
5. The Product Owner performs required interactive, visual, and business QA.
6. An approved task is squash-merged to `master` as one final commit.

---

# Documentation

| File | Purpose |
|------|---------|
| `AGENTS.md` | Development guidelines for AI assistants |
| `PRODUCT.md` | Product principles, concepts, and information authority |
| `ARCHITECTURE.md` | Current implemented architecture |
| `ADR.md` | Architecture Decision Records |
| `SPRINTS.md` | Sprint planning and progress |
| `ROADMAP.md` | Long-term development plan |
| `IDEAS.md` | Future ideas and technical debt |
| `COLLABORATION.md` | Roles, session bootstrap, model strategy, Git review, and QA workflow |

---

# Design principles

The project follows a few simple principles:

- Readability before cleverness.
- One responsibility per component.
- Small incremental changes.
- Preserve existing behaviour unless intentionally changed.
- Human-in-the-loop.
- Explicit state transitions.
- Test before refactoring.
- Architecture evolves through ADRs.
- Runtime data is stored separately from project information.

---

# Current limitations

The project is still evolving.

Known areas planned for future sprints include:

- Project creation and editable project metadata
- Improved Streamlit UX, layout, navigation, and feedback
- Additional artifact types and broader lifecycle support
- Dual DOCX/XLSX export for approved Riskanalys content

Sprint 6 also provides a reproducible local demo of the read-only Planning
Navigator and shared artifact lifecycle, including grounded CREATE/REFINE,
human acceptance of exports, baseline REFINE, clean-slate CREATE, and project
isolation. See [`doc/SPRINT6_DEMO.md`](doc/SPRINT6_DEMO.md).

---

# License

Educational project.
