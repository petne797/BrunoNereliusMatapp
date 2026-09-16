# AGENTS.md

> Repository rules for agents working on Projektassistenten. Keep this file short, practical, and revise it as we learn.

## Session bootstrap

At the beginning of every new Codex session that works in this repository, before making changes:

1. Verify that the working directory is the intended Projektassistenten repository.
2. Read `COLLABORATION.md` and follow the **Codex-bootstrap** defined there.
3. Read the relevant repository documentation required by the task.
4. Check `git status --short` and preserve unrelated user changes.

`COLLABORATION.md` defines the collaboration model, role boundaries, session bootstrap, and hand-off process between the Product Owner/business representative, Chat, Work, and Codex.

This `AGENTS.md` defines how agents work safely and consistently inside the repository.

## Purpose

This is an educational Python project for learning AI-agent development. The application is a command-line project assistant that reads project documents, calls Gemini tools, manages drafts, and generates Office documents.

The goal is both working code and a system that becomes easier to understand, explain, test, and extend.

## Collaboration and design

- Inspect the relevant code and runtime path before making changes.
- State important assumptions. If evidence contradicts an assumption, show the evidence, explain the consequence, and propose the smallest viable alternative.
- Challenge assumptions when they bypass approval or safety rules, create avoidable complexity, or introduce a second source of truth. Do not challenge preferences merely because another style is possible.
- Explain substantial architectural changes before implementation: current limitation, affected components, proposal, and trade-offs. Wait for approval unless the requested task already authorizes that exact change.
- Prefer small, reviewable improvements over broad rewrites. Preserve behavior unless changing it is part of the task.
- Keep unrelated cleanup out of the diff and report what was verified, what was not tested, and any uncertainty.

Prefer readable, explicit Python over clever or compact code. Choose the smallest design that solves the present problem cleanly. When two solutions work, prefer fewer concepts, fewer moving parts, and a clearer test. Simplicity means easy to understand and change, not merely fewer lines.

Avoid speculative abstractions, frameworks, compatibility layers, and generalization for hypothetical needs. Reuse an existing boundary when it fits, and add a dependency only for a concrete reason whose cost has been explained.

If you discover a larger architectural problem than expected, stop and explain it instead of solving it.

Complete one architectural slice at a time.

Prefer finishing one end-to-end workflow before generalising it to additional artifact types.

## Reuse before abstraction

### Reuse first

When implementing a new artifact, first attempt to reuse the existing platform:

- `ArtifactDefinition`
- `SourceSelector`
- Workflow
- `ArtifactContext`
- Generator
- `ArtifactDraft`
- `DraftStore`
- Markdown persistence
- `GeneratedDocument`
- `OfficeTemplate`
- Office renderers

Introduce new infrastructure only after the existing architecture has been
shown to be insufficient.

### Domain before presentation

Model the project domain first, for example `Risk`, `ScheduledActivity`,
`WBSNode`, or `Deliverable`. Word and Excel documents are presentations of the
domain. Renderers transform domain objects into Office layouts; Office layouts
must not dictate domain models.

### Extend before creating

When additional behaviour is needed, prefer extending an existing generic
component over introducing a parallel component. Extend boundaries such as
`GeneratedDocument`, `ArtifactDefinition`, `SourceSelector`, and
`OfficeTemplate` before creating artifact-specific infrastructure.

### Reference implementation

Risk Analysis is the reference implementation of the artifact lifecycle. New
artifacts should reuse its architecture without duplicating its code. Treat it
as the architectural example, not a template to copy.

## Learning principles

- Explain the important idea and trade-off behind a change, not every line of syntax.
- Connect relevant Python concepts to their role in the application.
- Distinguish intended architecture from the code path actually used at runtime.
- Make one conceptual improvement at a time when practical.
- Use tests as executable examples of expected behavior.
- Treat inconsistencies as learning opportunities: identify the cause and propose the next small step.
- Do not add sophistication solely to demonstrate a pattern or technology.

## Architecture and implementation

The repository is transitional. The live CLI path centers on `main.py`, `agent/project_assistant.py`, `agent/gemini_tools.py`, `agent/tool_executor.py`, and `agent/tools.py`. A newer artifact path uses definitions, workflows, source selection, `ArtifactContext`, `ArtifactDraft`, `DraftStore`, and generators. Improve or consolidate these paths incrementally; do not add a third path.

Maintain these boundaries:

- `gemini_tools.py` defines model-facing schemas; `tool_executor.py` allowlists Python callables.
- `document_store.py` manages source documents; `document_agent.py` builds model context.
- `ProjectDocument` represents source material, `ArtifactDraft` editable generated content, and `GeneratedDocument` file-ready output.
- Tool schemas, dispatch, implementation, prompting, state transitions, and file generation should remain separate where practical.
- Tool schema arguments must match their Python callable. Never execute arbitrary model-provided names.
- Preserve the human-in-the-loop flow: draft, review or revision, explicit approval, then generation.
- Do not treat `AI_INPUT` or AI drafts as authoritative decisions. Retain source metadata in generated output.
- Avoid silent overwrites and keep file-version behavior predictable.
- Prefer configured paths and `pathlib.Path` over machine-specific absolute paths.

When changing a tool, inspect its schema, allowlist entry, callable, CLI result handling, and draft or generation state.

Understand the runtime path before fixing a bug.

Prefer redirecting execution to the intended architecture over repairing obsolete execution paths.

## Testing and repository safety

- Read relevant tests before changing behavior; run focused tests first and the broader suite when shared behavior may be affected.
- Add tests for fixes, tool contracts, reusable logic, lifecycle transitions, and document flow.
- When task scope and architecture are decided, Codex works autonomously through implementation, normal debugging, focused tests, relevant regression, static checks, and preparation of a reviewable commit/PR. A failing test or ordinary technical error is not by itself a reason to stop: analyze the cause, make the smallest correct in-scope correction, add or adjust a regression test when needed, rerun the affected tests, and continue.
- Escalate decisions, not normal troubleshooting. Stop and report when a finding requires a new product decision, an architecture decision outside the decided scope, material scope expansion, risking unrelated user changes, external credentials or permissions, an environment limitation that genuinely prevents safe completion, or a choice the repository rules and facts cannot resolve. If you discover a larger architectural problem than expected, stop and explain it instead of solving it.
- Handle execution constraints first with established safe mechanisms, such as smaller test batches or supported selectors through `scripts/run_tests.py focused`; report an environment problem only when it actually prevents safe completion.
- Use temporary directories and synthetic documents. Mock Gemini calls so tests need neither network access nor credentials.
- Codex owns automated technical verification. Interactive UI use, human UX
  judgement, native/visual Office QA, and business-quality assessment normally
  belong to the Product Owner as defined in `COLLABORATION.md`.
- When native Office automation is explicitly part of a technical task on
  Windows, use the locally installed Microsoft Office applications through COM
  rather than LibreOffice. Use a separate application instance, open originals
  read-only or work on a temporary copy, close without saving, and never close
  Office windows that the user already had open.
- Do not claim success from inspection alone.
- Do not modify runtime files under `projects/`, commit, push, create branches,
  or rewrite history unless the task or explicit instruction authorizes it.
- Preserve unrelated user changes and keep temporary data, API keys, and other secrets out of the repository.

## Git workflow

- Keep the working tree clean between tasks.
- Complete one task at a time.
- For an authorized implementation task, work on its task-branch and publish
  reviewable commits to its lightweight GitHub PR; do not push unreviewed code
  directly to `master`.
- An authorized task normally continues through technical verification, commit,
  push, and a reviewable PR without intermediate approvals unless an escalation
  point above occurs.
- Report the branch, PR, exact review SHA, tests, and remaining risks.
- Apply review and QA corrections in the same Codex session, branch, and PR.
- After Chat review and required Product Owner QA are approved, squash merge so
  `master` receives one final commit for the accepted task.
- Do not start a new task with unrelated uncommitted changes.

## Repository documentation

Before making architectural proposals or implementing larger changes, read the project documentation:

1. README.md
2. ARCHITECTURE.md
3. ADR.md
4. ROADMAP.md
5. SPRINTS.md

Treat these documents as the primary description of the intended architecture.

If the code and documentation disagree, identify the discrepancy instead of silently choosing one.

## Definition of Done

Before considering a task complete:

- Relevant tests pass.
- Documentation is updated if architecture or behaviour changed.
- ADR is updated for architectural decisions.
- Sprint status is updated when appropriate.
- Working tree is clean or remaining changes are explained.
- New technical debt is documented in IDEAS.md if intentionally deferred.
