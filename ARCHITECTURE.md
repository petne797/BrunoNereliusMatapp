# Architecture

> Living architecture document.  
> Describe the current architecture, not the ideal future architecture.
> Update this document incrementally as the system evolves.

---

# Purpose

AI Project Assistant is an educational Python project for learning how to build AI agents using Google Gemini.

The assistant helps a project manager create and maintain project artifacts while keeping the human in control of all important decisions.

The project is intentionally developed in small architectural increments. Simplicity, readability and explicit responsibilities are preferred over clever abstractions.

---

# Architectural Principles

The project follows a small number of guiding principles.

## Human in the loop

AI creates drafts.

Humans review, revise and approve.

The assistant never makes project decisions on behalf of the user.

---

## Explicit state

Application state is owned by the application, not by the language model.

Examples:

- current project
- artifact drafts by artifact type
- approval status

The model receives the current state as context but does not own it.

---

## Single responsibility

Each component should have one primary responsibility.

Examples:

- Workflow coordinates business logic.
- Generator communicates with Gemini.
- DraftStore owns editable drafts.
- OfficeDocumentGenerator coordinates Word and Excel file generation.

---

## One source of truth

Each concept should have one canonical owner.

Examples:

| Concept | Owner |
|----------|------|
| Active project root | Canonically resolved `state.current_project_path` |
| Active project identity | Selected directory name |
| Project metadata | ProjectContext |
| Artifact drafts | Project-bound DraftStore |
| Source documents | DocumentStore |
| Generated files | document_generate |

Avoid duplicate representations of the same information.

---

## Incremental evolution

Architecture evolves one vertical slice at a time.

Finish one complete workflow before generalising it.

---

## Sprint 2 platform principles

Risk Analysis is the reference implementation of the artifact lifecycle. It is
not intended to remain a special case. New artifacts should reuse the existing
lifecycle and extend its artifact-specific parts instead of duplicating state,
approval, persistence, or export infrastructure.

The canonical lifecycle is:

```text
ArtifactDefinition
→ Workflow
→ ArtifactContext
→ Generator
→ ArtifactDraft
→ DraftStore
→ Markdown persistence
→ Approval
→ GeneratedDocument
→ Office renderer
→ Export
```

Sprint 4 validated this lifecycle for two additional structured artifacts.
Artifact-specific domain objects, codecs, generator rules, source validation,
and presentation renderers remain appropriate where semantics differ; they do
not constitute a parallel lifecycle. There is currently no demonstrated need
for a general `StructuredArtifact` base, universal codec, generic artifact
engine, or shared Goal/Deliverable/WBS domain model. Such an abstraction should
only be introduced after several concrete contracts expose the same stable
need.

---

# High-level Architecture

```
                User

                  │

                  ▼

      CLI / local Streamlit demo

                  │

                  ▼

ProjectAssistant / public application boundary

                  │

                  ▼

ToolExecutor (model calls) / public draft API

                  │

                  ▼

       Workflow / draft lifecycle

                  │

                  ▼

       AI Generator when needed

                  │

                  ▼

           ArtifactDraft

                  │

     Review / Revision / Approval

                  │

                  ▼

      GeneratedDocument

                  │

                  ▼

     OfficeDocumentGenerator

                  │

                  ▼

          DOCX / XLSX File
```

---

# Runtime Flow

The Risk Analysis reference implementation path is:

```
User

↓

Gemini

↓

Tool selection

↓

ToolExecutor

↓

PlanningCreateRiskAnalysisWorkflow

↓

SourceSelector

↓

RiskAnalysisGenerator

↓

ArtifactDraft

↓

DraftStore

↓

CLI presentation

↓

Revision

↓

Approval

↓

GeneratedDocument

↓

WordGenerator

↓

DOCX
```

This remains one supported runtime path. Tidplan, Mål, and Leveranser reuse the
same lifecycle boundaries with their own domain, codec, source, validation,
and presentation rules. The local Streamlit demo enters through
`ProjectAssistant` for natural-language requests and the same public draft API
for explicit controls; it does not create a parallel lifecycle. Office export
uses the template-selected Word or Excel renderer as described below.

---

# Domain Model

The project distinguishes clearly between different document concepts.

```
ProjectDocument

↓

DocumentContext

↓

ArtifactContext

↓

ArtifactDraft

↓

GeneratedDocument
```

## ProjectDocument

Represents existing project documentation.

Examples:

- Project plan
- Meeting notes
- Decision documents
- Status reports

Project documents are read-only from the AI's perspective.

---

## DocumentContext

Wraps the complete document list available to a generation task.

`SourceSelector` selects the relevant sources from this list and passes them to `ArtifactContext`.

---

## ArtifactContext

Represents everything needed to generate an artifact.

Contains:

- artifact definition
- selected sources
- instructions
- template
- rules
- scenario

ArtifactContext is internal and never presented directly to the user.

---

## ArtifactDraft

Represents editable AI-generated content.

Characteristics:

- mutable
- may be revised
- stored in DraftStore
- persisted as Markdown in `AI-utkast`
- not yet approved

---

## GeneratedDocument

Represents approved output ready for export.

GeneratedDocument is the boundary between the domain model and document generation.
It carries the resolved `OfficeTemplate` selected by the artifact definition.

Its two content representations have distinct purposes:

```text
GeneratedDocument.content
→ approved textual representation and text-oriented rendering

GeneratedDocument.structured_content
→ typed domain data for structured Word or tabular rendering
```

`content` remains the required approved textual representation.
`structured_content` is an optional read-only mapping at the export boundary
and is not part of `ArtifactDraft` or Markdown persistence. Text-oriented Word
artifacts consume `content`; a structured Word presentation or Excel renderer
may consume explicit typed `structured_content`. A renderer must never recover
domain values by interpreting arbitrary Markdown or prose.

`structured_content` represents domain data, not Office layout. Tidplan is the
first structured example. Its preferred conceptual representation is:

```text
GeneratedDocument.structured_content
├── groups
│   └── sequence of ScheduleGroup domain values
└── activities
    └── sequence of ScheduledActivity domain values
```

The Excel renderer maps groups and activities to worksheet rows. It is only a
presentation and never a persistent source of truth:

```text
groups + activities
→ worksheet rows
```

The domain owns meaning. The renderer owns presentation.

For Tidplan, `ScheduleGroup` is a separate, non-recursive high-level
orientation concept, shown as Huvudaktivitet. It has a stable `TPG-...` ID,
title, optional description and explicit order; it is not a WBS hierarchy.
`ScheduledActivity` objects remain the in-memory activity values and may hold
an optional `schedule_group_id` reference to one ScheduleGroup in the same
Tidplan. Their enums, dates, progress, WBS relation and dependencies retain
their existing domain semantics. Group dates are never persisted. Presentation
may derive a group's interval from the minimum member start date and maximum
member end date; a preliminary group without members has no dates.

The models provide deterministic mappings with enum values and ISO dates for
the codec boundary. An Excel renderer may consume the values directly;
worksheet rows, formulas, colors, week columns and Gantt layout remain renderer
concerns.

`ArtifactDraft` remains text-oriented. Tidplan uses a strict content contract:
`ArtifactDraft.content` contains exactly the `# Tidplan` heading and one visible
YAML block with `groups`, `activities` and `open_questions`. That Markdown file
is the single persistent source of truth; there is no structured sidecar and no
`structured_content` in `ArtifactDraft`. Earlier canonical Tidplan files with
only `activities` and `open_questions` remain readable during restore without
rewriting and migrate only when subsequently canonicalized.

Projektplan is the complementary prose contract: `ArtifactDraft.content` is
strict sectioned Markdown with exactly one canonical title and ordered H2
sections. Its small artifact-specific codec owns only that document skeleton;
section bodies stay ordinary Markdown and do not duplicate the domain models of
the seven specialised planning artifacts. The Markdown in
`AI-utkast/Projektplan.md` is the sole persistent truth. A later DOCX is a
presentation, not another truth source. Projektplan is intentionally absent
from Navigator candidates until Sprint 8 Task 4.

The Tidplan codec parses the content, constructs and validates `ScheduleGroup`
and `ScheduledActivity` values, validates group references and cross-activity
dependencies, and
serializes the result deterministically back to canonical Markdown. Generation
and revision validate and canonicalize the Gemini response before changing
DraftStore or Markdown persistence:

```text
Generation or revision
→ Tidplan codec validation
→ canonical ArtifactDraft.content
→ DraftStore
→ AI-utkast/Tidplan.md
```

Restore validates Tidplan content without rewriting the file. An invalid or
older free-form Tidplan is reported and skipped without preventing other valid
drafts from being restored. Approval uses the same codec and additionally
requires at least one activity and no unresolved `open_questions`.

### Sprint 7 Intressentanalys core contract

Intressentanalys is a complete structured planning slice in the shared
lifecycle. Its presentation-independent immutable core, generator, CREATE and
REFINE workflow, strict draft validation, persistence, restore, approval and
XLSX export reuse the established boundaries. `StakeholderAnalysis` retains the supplied
order of unique `STK-...` `Stakeholder` values and structured `STK-Q-...` open
questions. A stakeholder has separate Intresse, Inflytande, Inställning and
Engagemang axes, final Prioritet and Strategi, handling fields and structured
source references. It has no hierarchy or automatic segmentation.

`name` and `description` are required text so that every stored stakeholder
remains identifiable and understandable. `needs_expectations`,
`additional_information`, `action_plan` and `responsible` may instead be
unknown (`None`) without implying or creating an open question. Canonical YAML
serializes those unknown text values as `value: null`; blank text remains
invalid. Enum axes retain their separate explicit `UNKNOWN` values.

`determine_stakeholder_baseline()` is a pure domain function: the four known
Intresse×Inflytande combinations return the fixed priority/strategy baseline;
an `UNKNOWN` input returns no baseline. Attitude and engagement do not affect
that result. The final values deliberately remain allowed to differ from the
baseline for a later human or AI recommendation.

The sole draft representation is strict `# Intressentanalys` Markdown with one
YAML block containing `stakeholders` and `open_questions`. Each stakeholder
field is represented as `{value, provenance}`. The closed provenance enum is
`ESTABLISHED`, `AI_SUGGESTION` or `AI_ASSESSMENT`; it is typed in the domain,
visible in the draft and deterministically retained by the codec.
`ESTABLISHED` means only that a value is no longer labelled as an AI hypothesis;
it does not claim a document source. Document traceability remains separately
represented by `source_references`, which may be empty. This small
artifact-specific wrapper is not a general product-wide provenance framework.
The codec rejects duplicate YAML keys, unknown fields, malformed markers,
invalid enum or ID values, malformed source references and malformed
questions. It has no filesystem, project, lifecycle or Office dependency.
Lifecycle validation canonicalizes and validates on create/update/restore and
strictly revalidates on approval and export. Approval means approved for
export; it neither changes provenance nor blocks or removes open questions.

Stakeholder transitions also have an artifact-specific identity guard before
DraftStore or Markdown mutation. `update_draft` compares the previous and new
canonical `StakeholderAnalysis` directly. Accepted-baseline REFINE reads only
the `ID` and `Intressent` cells from the already loaded text projection of the
known exported main table and compares them with the new canonical result.
Exact names are normalized with Unicode compatibility normalization,
case-folding and collapsed whitespace; an unchanged normalized name may not
change STK-id, and an unambiguous ID swap is rejected. Deletion, a new name
with a new ID and a retained ID with a clear name change remain possible.
There is no fuzzy entity resolution or general XLSX-to-domain restore path. If
a manually authored normal workbook does not expose the known main-table
header safely, REFINE cannot deterministically match its identities and the
remaining stable-ID behavior is prompt-controlled.

At export, the approved canonical Markdown remains `GeneratedDocument.content`
and the codec reconstructs transient
`structured_content["stakeholder_analysis"]`. The allowlisted stakeholder
Excel renderer consumes that typed value and never parses Markdown. The
template-driven workbook contains `Intressentanalys`, `Intresse-Inflytande`
and `Öppna frågor`: the main table exposes every user field, provenance and
source references; four known Interest×Influence quadrants are derived from
the domain values; and UNKNOWN values remain visibly unplaced. Excel is a
presentation, never a second persistent truth.

`create_generated_document()` is the `ArtifactDraft` → `GeneratedDocument`
integration point. For Tidplan it uses the same codec and repeats the approval
validation before constructing the document. The approved Markdown in
`GeneratedDocument.content` is preserved byte-for-byte; it is neither
normalized nor serialized again. The structured representation is:

```text
{
    "groups": tuple(parsed_groups),
    "activities": tuple(parsed_activities),
}
```

Each tuple entry is a validated domain value; group order and activity order
match the approved draft. An empty activity collection, unresolved
`open_questions`, non-canonical content, or invalid domain values stop the
conversion before renderer selection and file output. No Gemini call,
DraftStore update, or Markdown write occurs at this boundary. The Excel
renderer receives explicit structured content and never parses Markdown.

### Sprint 4 Mål contract and draft lifecycle

Sprint 4 reuses the same strict-text pattern without adding structured state to
`ArtifactDraft`, a sidecar file, or a parallel lifecycle. The Mål codec parses
and serializes one canonical `# Mål` Markdown document with exactly
one visible YAML block. The conceptual persistent shape is:

```yaml
project_commitment: null
goals:
  - id: E-001
    type: Effektmål
    title: Förbättrad verksamhetsnytta
    description: Ett styrande önskat tillstånd.
    success_criteria: []
    goal_areas: []
    source_references:
      - document: Projektdirektiv
        location: null
  - id: P-001
    type: Projektmål
    title: Ett tydligt projektresultat
    description: Ett resultat som projektet ansvarar för.
    success_criteria: []
    goal_areas: []
    source_references: []
    contributes_to_effect_goal_ids: [E-001]
    target_date: null
change_proposals: []
open_questions: []
```

`project_commitment` is optional artifact-level context for project idea or
commitment and result-, time-, or cost frame; it is not a Goal. `goals` is one
collection with explicit `type`, not separate persistent collections for
Effektmål and Projektmål. Type-specific validation requires authoritative
`source_references` for Effektmål and permits only Projektmål to reference
existing Effektmål through `contributes_to_effect_goal_ids`. A change proposal
remains separate from currently governing goals.

Mål now uses the existing draft lifecycle up to approved persistent
`ArtifactDraft` state:

```text
create_goal
→ PlanningCreateGoalWorkflow
→ ArtifactDefinition / SourceSelector / ArtifactContext
→ GoalGenerator
→ Goal codec
→ ArtifactDraft
→ DraftStore and AI-utkast/Mål.md
→ show / revision / restore / approval
```

The shared strict-draft validation boundary canonicalizes Mål revisions before
they replace DraftStore or the Markdown file. It validates canonical content
and source-reference support again during persistence and restore, without
rewriting a valid restored file. Approval uses the same validation but permits
`open_questions`; approval is only approval for a later export, not SMART or
PPS acceptance. Tidplan retains its stricter approval rule for unresolved
questions.

Approved Mål drafts continue through the export boundary:

```text
approved ArtifactDraft
→ existing Goal approval and source validation
→ create_generated_document()
→ unchanged canonical Markdown in GeneratedDocument.content
→ GoalArtifact in structured_content["goal_artifact"]
→ transient GoalWordViewModel
→ WordTemplateRenderer
→ allowlisted GoalWordContentRenderer
→ versioned DOCX
```

`GoalWordViewModel` references the canonical Goal objects and derives only
presentation projections: deterministic Effektmål and Projektmål collections,
Effect Goal groups, unlinked Project Goals, compact goal-area labels,
conservative SMART evidence/gaps, and the many-to-many relationship matrix.
These projections are never persisted. Each Project Goal has one canonical
detail view even when it appears as a compact reference under several Effect
Goals.

`WordTemplateRenderer` still owns template opening, exact top-level marker
validation, title and metadata, source presentation, template immutability,
and saving. Its optional `renderer_config.body_renderer` is resolved through
an internal allowlist. Missing configuration retains the existing Markdown
body renderer used by Riskanalys; `body_renderer: goal` delegates only the
dynamic content marker to `GoalWordContentRenderer`. Configuration cannot name
an import path or executable callable.

The approval validator distinguishes blocking canonical or domain errors
from warnings. Duplicate IDs, invalid types, dangling relationships, and an
Effektmål presented as governing without a valid source block approval. Open
questions about incomplete planning information are warnings and do not alone
block export. `approved` remains an export state, not SMART acceptance or
planning completion. SMART is a derived quality view from canonical Goal data,
source support, and open questions; no five persistent AI boolean fields or
separate SMART presentation data are used.

The production `templates/goals.docx` is an ordinary marker-compatible
`OfficeTemplate` adapted from the approved visual direction. It is a clean
presentation shell and contains no repeated example goals. The dynamic Goal
renderer creates overview tables, Effektmål grouping, canonical Projektmål
detail blocks, SMART evidence/gaps, open questions and change proposals, and
the derived relation matrix. The external Word proposal remains design input
only and is not read at runtime.

### Sprint 4 Leveranser lifecycle and Word export

Leveranser follows the same strict-text and shared-lifecycle principles as
Mål and Tidplan. `Deliverable` represents what the project is expected to
deliver through `id`, `title`, `description`,
`acceptance_criteria`, `recipients`, `target_groups`, `source_references`, and
optional stable Goal relations in `contributes_to_goal_ids`. `recipients`
records the organizational receiver expected to take responsibility after
handover, while `target_groups` records users or groups expected to use or
benefit from the result. The concepts remain separate and may be empty when
unknown. It contains no owner, status, date,
dependency, activity, milestone, WBS, or scheduling state.

The canonical persistent shape is one visible `# Leveranser` Markdown document
with exactly one YAML block:

```yaml
deliverables:
  - id: L-001
    title: Ett verifierbart projektresultat
    description: Det resultat som projektet ska leverera.
    acceptance_criteria: []
    recipients: []
    target_groups: []
    source_references: []
    contributes_to_goal_ids: []
open_questions: []
```

The codec reconstructs immutable `Deliverable` and `DeliverableArtifact`
values, rejects malformed or non-canonical structures, and serializes field
order deterministically. Duplicate Goal relations are intrinsically invalid.
`AI-utkast/Leveranser.md` is the sole persistent representation; no sidecar,
structured `ArtifactDraft`, or parallel store is used.

The supported runtime path is:

```text
create_deliverables
→ PlanningCreateDeliverablesWorkflow
→ ArtifactDefinition / SourceSelector / ArtifactContext
→ DeliverableGenerator
→ strict Deliverable codec
→ ArtifactDraft / DraftStore / AI-utkast/Leveranser.md
→ show / revise / restore / approval
→ create_generated_document()
→ transient DeliverableArtifact in GeneratedDocument.structured_content
→ WordTemplateRenderer
→ allowlisted DeliverableWordContentRenderer
→ versioned DOCX
```

Source selection is conditional. A current normal `Måldokument` is an
important source when available, but Mål is not a creation prerequisite and
`AI-utkast/Mål.md` is excluded. The current selected-source representation can
verify that a normal Måldokument was selected, but it exposes document content
as text rather than a machine-verifiable Goal-ID index. Consequently external
validation rejects Goal relations when no verified Måldokument is selected;
when one is selected, the generator is constrained to explicit IDs supported
by that source, but runtime does not claim stronger exact-ID validation than
the boundary can prove. Recipient grounding is semantic within selected
project-source content: a named function with stated post-project ownership,
management, operation or long-term responsibility is sufficient even without
the literal word "recipient". Unknown relations and recipients remain empty
and are reported as open questions; a known recipient is retained when only
additional responsibility remains unresolved.

Approval revalidates canonical structure and source references but permits
open questions during iterative planning. Approved Markdown is preserved
unchanged in `GeneratedDocument.content`; the typed `DeliverableArtifact` is
reconstructed only at the export boundary and is never persisted.

The production `templates/deliverables.docx` is a clean marker-compatible
`OfficeTemplate`. `body_renderer: deliverable` is resolved through the same
closed allowlist as Goal; arbitrary renderer imports are impossible.
`DeliverableWordContentRenderer` owns only Deliverable-specific presentation:
a compact overview, one canonical detail block per Deliverable, recipients,
acceptance criteria, separate recipients and target groups, Goal relations,
source traceability, and open questions.
It adds no presentation fields to the domain, prevents detail rows from
splitting across pages, and never parses Markdown. Export does not modify the
template.

### Implemented Sprint 5 WBS contract and Tidplan interaction

WBS is a supported runtime artifact. It describes what the project's work
consists of through an implicit project root and a flat, canonically
depth-first ordered collection of typed `WBSNode` values with explicit
`parent_id`. Stable IDs such as `WBS-001` are independent of hierarchy,
position and node type; display numbers are derived presentation values. The
domain permits different branch depths and has no Excel-imposed maximum depth.

The node types are `Arbetsområde` and `Arbetspaket`; a work package is always a
leaf. Nodes retain source references and may each have 0..N direct
`contributes_to_deliverable_ids`. `responsible: str | None` and
`estimated_effort_hours: int | None` are work-package-only fields. WBS has no
canonical activities, schedule dates, status, progress or dependencies;
`ScheduledActivity` remains Tidplan's domain model and owns the optional
`wbs_work_package_id` relation.

The persistent contract is one strict `# WBS` Markdown document with exactly
one visible YAML block containing `nodes` and `open_questions`. The codec
reconstructs a transient `WBSArtifact`, validates hierarchy and type-specific
invariants, and keeps Markdown as the only persistent content source of truth.
Ordinary approval requires a valid hierarchy, at least one top-level node and
work package, children for each work area, and no blocking open questions. It
does not claim to prove 100-percent scope coverage.

WBS → Deliverable uses a WBS-specific, transient source-aware grounding
boundary. It starts from the already selected WBS source set and chooses one
normal Leveransdokument deterministically: `STYRANDE/Gällande` before
`PROJECT_ARTIFACT/Projektunderlag`, with fail-closed ambiguity at the highest
available level. The source path is resolved under the explicit canonical
active project root before the DOCX is opened; absolute external paths,
relative escapes, missing paths and stale roots cannot authorize relations.

The reader does not search arbitrary Word text. It requires exactly one
generated overview table with `ID`, `Leverans`, `Mottagare` and
`Bidrar till Mål`, then cross-checks its ordered ID/title pairs against the
generated `ID – title` detail headings. Duplicate IDs, conflicting entries,
unexpected structure and broken DOCX files yield no grounding. The resulting
immutable ID/title entries exist only in memory; Task 8B uses their exact IDs
for prompt grounding and validation. At the GeneratedDocument/export boundary,
the same validation operation returns both the approved `WBSArtifact` and the
grounding it used, so the source is read once per operation and no second title
lookup or source of truth is introduced.

CREATE resolves grounding before its single Gemini call, includes the exact
allowed IDs in the prompt and validates the response against the same set.
REFINE reloads and reselects current WBS sources first. Shared draft
validation passes the explicit project root through persistence and restore;
approval and `create_generated_document()` reopen the source before status or
export mutation. No hidden DraftStore read, AI-Input, AI draft, Export file,
sidecar, global ID index or database bypasses this boundary. WBS domain and
codec parsing remain presentation- and project-independent.

Task 8D adds one narrow policy exception after those validations. Ordinary
`approve_draft()` still rejects a WBS with `blocking: true` and returns every
remaining question plus a recommendation to resolve them. The deterministic
CLI and Streamlit presentation can then call the separate, non-Gemini,
non-allowlisted `approve_wbs_with_open_questions()` adapter only after the
explicit action `Godkänn och exportera ändå som utkast`. The adapter records
`ArtifactDraft.wbs_open_questions_override: bool` while retaining the existing
`approved` lifecycle status. Persistence writes the field only when true, and
restore validates its boolean type, WBS scope and approved status.

At both approval and GeneratedDocument boundaries, an explicitly requested
exception still runs canonical schema, hierarchy, type, project-root,
source-reference and exact Deliverable ID/title grounding validation before
status or file mutation. Ordinary approval retains its established
question-first error. Export accepts the recorded exception only while a
blocking question actually remains. The exception never changes canonical WBS
Markdown or `WBSOpenQuestion.blocking`; consequently the existing renderer
receives and preserves every question on `Öppna frågor`.
Other artifact types cannot set or consume the field, and the Gemini
`approve_draft` schema and tool allowlist expose no override argument or tool.

Task 8A keeps the WBS codec and domain unchanged while making planning context
current and bounded. `DocumentStore` can atomically replace its index, and WBS
REFINE explicitly reloads it before applying the existing WBS definition and
`SourceSelector`. Both CREATE and REFINE pass selected sources through the same
deterministic WBS-specific projection: approximately 2,000-character chunks,
at most 8,000 content characters per source and 32,000 in total. Small sources
remain unchanged; the first chunk and later chunks relevant to scope,
deliverables, responsibility, roles, resources, estimates and decomposition
are prioritized with stable ordering and tie-breaking. Projection neither
calls Gemini nor reads paths; it consumes only already indexed source content.

The common document boundary also accepts `.txt` through a strict UTF-8/
UTF-8-BOM reader with a 1 MiB limit and NUL-byte rejection. Per-file failures
remain isolated during indexing, empty files and runtime folders remain
excluded, and an unsuccessful atomic reload leaves the previous in-memory
index intact. WBS REFINE validates and canonicalizes the single Gemini result
against the newly selected sources before changing Markdown or DraftStore.
The resulting draft persists current source metadata but never the projected
source content.

The implemented export path remains within the established lifecycle:

```text
approved WBS ArtifactDraft
→ WBS codec and approval validation
→ create_generated_document()
→ unchanged canonical Markdown in GeneratedDocument.content
→ transient WBSArtifact and immutable Deliverable ID/title grounding
  in structured_content
→ allowlisted WBS-specific Excel renderer
→ versioned XLSX
```

The WBS structured-content contract has exactly three keys:

```text
{
    "nodes": tuple(validated_wbs.nodes),
    "open_questions": tuple(validated_wbs.open_questions),
    "deliverable_grounding": tuple(DeliverableGroundingEntry),
}
```

`DeliverableGroundingEntry` is frozen and contains only `id` and `title`.
The projection is presentation-only and is never added to `WBSNode`, the WBS
codec, Markdown, DraftStore or source metadata. The renderer validates the
exact key set, tuple and entry types, duplicate IDs, and title coverage for
every used relation before opening the workbook. It receives no project root
and never selects or opens a Leveransdokument.

The WBS production template has one main worksheet with one row per WBSNode,
derived display number, stable ID, node type, indented title, description,
Deliverable `ID – title` values in node ID order, work-package responsibility
and effort, and source references.
Area effort totals and open-question views are derived presentation data. The
allowlisted WBS renderer is selected through a closed identifier in
`OfficeTemplate.renderer_config`; it cannot name an arbitrary import path or
callable.

Tidplan's WBS-aware boundary selects only normal WBS sources with the exact
lifecycle combinations `STYRANDE/Gällande` or
`PROJECT_ARTIFACT/Projektunderlag`, prioritizing the former. Only one unique,
schema-identified WBS-XLSX at the highest level can authorize relationships.
The source workbook is reopened read-only and its `WBS` sheet, ID/type/title
headers and relevant rows are validated into transient work-package and
work-area sets. These sets ground the CREATE and REFINE prompts and validate
every non-null relationship before persistence or DraftStore mutation. They
are never persisted as an index.

Before the workbook is opened, its absolute resolved path must be contained by
the explicit active project root. Relative source paths are resolved against
that root; neither process cwd, lifecycle-looking folder names nor editable
draft source metadata can establish or widen the boundary.

With no machine-verifiable WBS, Tidplan remains usable but every relationship
must be `null`. Equal-priority ambiguity or broken XLSX grounding is exposed as
an open question. REFINE uses current indexed project context and refreshes WBS
source metadata; restore, approval and GeneratedDocument reconstruction reopen
the recorded normal source and fail closed for orphaned, unknown or work-area
references. `ScheduledActivity` and the Tidplan codec continue to validate only
ID syntax, preserving the domain/source-boundary separation.

---

## OfficeTemplate

Represents the presentation template for an exported artifact.

It contains:

- the resolved path to an ordinary Office document
- optional renderer configuration

`OfficeTemplate` does not store a separate format value. Future renderers select
their implementation from the template file extension. Initially supported
extensions are `.docx` and `.xlsx`.

`ArtifactDefinition` selects the template. `GeneratedDocument` carries it across
the output boundary. `ArtifactDraft` remains independent of layout, styles, and
Office format.

`OfficeTemplate` is the shared abstraction for Office output. Renderer selection
is based on the template suffix. Word and Excel deliberately use
format-specific rendering strategies internally; the architecture does not
require them to share a placeholder mechanism.

---

# Component Responsibilities

## main.py

Application entry point.

Responsibilities:

- start application
- initialise services
- hand control to ProjectAssistant

---

## ProjectAssistant

Conversation orchestration.

Responsibilities:

- communicate with Gemini
- execute selected tools
- present results
- coordinate conversation flow

---

## Gemini Tool Definitions

Defines model-visible tool schemas.

Responsibilities:

- tool names
- descriptions
- parameters

Contains no business logic.

---

## ToolExecutor

Safe dispatch layer.

Responsibilities:

- explicit allowlist
- execute Python callables
- prevent arbitrary execution

---

## Workflow

Coordinates business logic.

Example:

PlanningCreateRiskAnalysisWorkflow

Responsibilities:

- build ArtifactContext
- coordinate source selection
- invoke generator

Workflow does not generate text itself.

---

## Generator

Communicates with Gemini.

Responsibilities:

- build prompts
- call Gemini
- construct ArtifactDraft

In the current architecture, `RiskAnalysisGenerator` also saves the generated `ArtifactDraft` to `DraftStore`. This is a temporary responsibility retained from Sprint 1 and should not be treated as the intended general generator contract.

Generators should contain AI interaction only.

---

## DraftStore

Owns the editable drafts for the active project.

Responsibilities:

- `ArtifactDraft` objects keyed by `artifact_type`
- draft status through `ArtifactDraft.status`
- draft lifecycle

DraftStore remains the owner of runtime draft state in memory. It exposes
`save(draft)`, `get_draft(artifact_type)`, `list_drafts()`, and `clear()`.
There is no separate active or current artifact.

Each `ArtifactDraft` is persisted as a Markdown representation in the active
project's `AI-utkast` folder after creation, revision, and approval. The
artifact type is the stable identity and determines the Markdown filename.

When a project becomes active, all valid persisted Markdown drafts are restored
into DraftStore before the next Gemini tool-selection request. A corrupt draft
is reported without preventing other valid drafts from being restored.

---

## Document Generation

Responsible for exported files only.

Responsibilities:

- GeneratedDocument
- Office document creation
- version handling

`OfficeDocumentGenerator` selects the export path from the resolved template
extension. A configured `.docx` template delegates to the existing
`WordGenerator` and `WordTemplateRenderer`. A configured `.xlsx` template
requires `structured_content` and delegates through a closed renderer key to
the Tidplan, WBS or Intressentanalys renderer.

The Word renderer opens the ordinary `.docx` template and saves a separate file
in `Export/`. It never modifies the template. Exact marker paragraphs define the
insertion points for title, project metadata, generated Markdown content, and
source metadata. Marker text comes from `OfficeTemplate.renderer_config`, and
every configured marker must occur exactly once as its own paragraph.

For artifact definitions without `office_template`, `WordGenerator` temporarily
keeps the existing blank-document fallback. A configured template that is
missing, invalid, unsupported, or has invalid markers fails clearly and never
falls back silently.

When an Office template exists, the output filename extension comes from that
template. Spreadsheet export never parses Markdown, JSON, or arbitrary text
from `content` into rows.

`ExcelTemplateRenderer` opens the ordinary `.xlsx` template and writes a new,
versioned workbook to `Export/`. Its renderer configuration identifies the
worksheet, a prototype row, the first output row, and the field-to-column
mapping. Each structured row copies the prototype row's styles, number formats,
alignment, borders, fill, font, protection, and row height. Formulas are
translated to the corresponding output row. Unconfigured workbook structure
and static content are preserved, and the source template is never modified.
When a configured `progress` cell uses a percentage number format, the
renderer converts the domain's 0–100 progress value to Excel's 0–1 percentage
value before writing it.

For a template that declares an `activity_area`, the renderer clears the
configured cell range before writing the current activities, then applies any
configured row-relative helper formulas. A weekly `timeline` configuration can
set the timeline's first date and week number from the earliest activity date.
The renderer requests full recalculation on open.

The Office template is itself a validated production resource. It owns the
workbook layout, formatting, empty prototype row, timeline formulas, and valid
worksheet-local names used by conditional formatting. `renderer_config` owns
the explicit mapping between structured activity fields and that layout,
including field value types, the output area, and helper formulas.
`ExcelTemplateRenderer` applies that contract deterministically but does not
repair template-specific defined names or other malformed workbook metadata.
It uses openpyxl's workbook API only; no raw OOXML manipulation is part of the
rendering path.

The renderer rejects missing or malformed structured content and does not infer
spreadsheet data from `GeneratedDocument.content`.

---

# Application State

The application maintains explicit state outside the language model.

Current state includes:

```
state.current_project_path

↓

Canonical active project root

↓

ProjectContext

↓

Project metadata

↓

DraftStore

↓

ArtifactDrafts keyed by artifact_type
```

`state.current_project_path` holds the canonically resolved active project
root. The selected directory name is the runtime project identity.
`ProjectContext` reads `metadata.json` only from that already selected exact
root. Its deprecated `name` field is legacy metadata ignored by runtime: it
cannot select a project, replace the directory identity or redirect
DraftStore, restore, source context, approval or export. A mismatch does not
block activation and metadata is never rewritten automatically.

Project selection is an exact direct-child lookup in the configured projects
root. Prefixes, substrings, first matches and process cwd are not project
selection mechanisms. A model-provided `project_name` is only a consistency
assertion: CREATE adapters require an exact match with the already active
project and cannot use it to select another root.

On activation, `DraftStore` is cleared and bound to the canonical root and
directory identity before persisted drafts are restored. Restore scans only
that root's `AI-utkast` and rejects a Markdown draft whose stored `project`
does not match the active identity. Draft selection, revision, approval and
export fail closed if the store binding no longer matches
`state.current_project_path`. The global `DocumentAgent` is recreated for the
same canonical root, and CREATE/REFINE reject a stale document index before
workflow or persistence mutation. Switching projects in one process therefore
replaces both draft state and source context instead of retaining stale data.

The current runtime has no project-creation or rename workflow. Removing
legacy `name` fields from existing metadata is a separate migration. A future
rename feature needs a controlled migration flow and should introduce a
stable `project_id` before storage location and display name can be decoupled.

Gemini should receive the relevant state before tool selection but never own it.

The runtime-state snapshot contains a deterministic draft inventory with
artifact type and status only. It never contains draft or source content. If
several drafts exist and the user does not identify one, Gemini must ask for a
clarification rather than guess.

The public draft API is:

```text
show_draft(artifact_type=None)
update_draft(question, artifact_type=None)
approve_draft(artifact_type=None, comment="")
generate_document(artifact_type=None)
```

The WBS presentation layers additionally use the explicit application adapter
`approve_wbs_with_open_questions(artifact_type="WBS", comment="")`. It is not a
Gemini tool and is valid only for a WBS that still has blocking open questions.

Omitting `artifact_type` is accepted only when exactly one draft exists.

## Local Streamlit demo

`streamlit_app.py` is a local presentation layer over the same project
activation, ProjectAssistant interaction, public draft API, DraftStore,
approval, persistence, and Office export boundaries used by the CLI. It does
not call generators or renderers directly and does not own a separate draft
state. Its closed allowlist exposes exactly Riskanalys, Mål, Leveranser, WBS,
and Tidplan. For an explicit artifact action, Streamlit derives the valid
action from project-phase capability plus DraftStore status and dispatches
directly to the existing public create, show, update, approve, or export
adapter. The generic Gemini tool-selection path is used only by the separately
labelled free dialogue. Optional create input is carried by the public create
adapter into the selected workflow's `ArtifactContext.instructions`; it is
therefore generator input within the already selected artifact, never a
routing mechanism.

Streamlit `session_state` holds only temporary UI selections, display history,
the active application reference, automatic-display markers, and returned
export paths. Project-scoped temporary values are bound to the active project
and artifact state and cleared when that context changes. The CLI and
Streamlit therefore share the same lifecycle and project-isolation contracts.
The UI state matrix is `missing → create`, `AI_DRAFT → show/refine/approve`,
and `approved → show/refine/export`. Refining an approved draft creates a new
`AI_DRAFT`; approval does not export automatically. Automatic draft display
and export presentation remain transient UI behavior and create neither new
domain state nor a persistent source of truth.
For WBS, a failed ordinary approval exposes all questions before either UI
offers a separate `Godkänn och exportera ändå som utkast` action. Cancellation
clears only temporary presentation state; approval state, DraftStore and export
files remain unchanged.

Streamlit är ett tillfälligt lokalt demonstrationsgränssnitt, inte den
planerade slutliga webbarkitekturen. När UI/UX-arbetet tas upp senare är Svelte
det valda frontendramverket; SvelteKit kan då utvärderas som applikationsram.
Frontend ska använda en tydlig applikations-/API-gräns mot Python-systemet och
får inte duplicera domänlogik, state, persistens, approval eller
Office-rendering. CLI:n kan fortsatt finnas parallellt. API-, autentiserings-,
driftsättnings- och frontendbeslut hör till en senare avgränsad insats och kan
kräva ett ADR.

## Implemented Planning Navigator

Sprint 6 Tasks 2–4 accepted and implemented the Planning Navigator's product
and architecture contract. The domain model lives in
`agent/domain/planning_navigator.py`, the analysis services in
`agent/planning_navigator.py`, the bounded Gemini ranker behind its interface,
and the public application and Streamlit integration at their existing
presentation boundaries. Task 5 retains human product review and demo QA.

Task 4B's accepted planning slice exposes baseline REFINE and clean-slate
CREATE through the existing workflow, SourceSelector, ArtifactContext,
DraftStore and atomic Markdown persistence boundaries. Approved drafts and
Export remain outside normal source selection; human review and placement of
an accepted export in `Projektplanering` are required.

The same explicit user-directive contract is appended to the ordinary
`update_draft()` prompt used by the Streamlit `Revidera` action. Thus normal
REFINE, baseline REFINE and CREATE share the distinction between
user-directed draft content and source evidence, while validation remains at
the existing artifact-specific boundaries.

All seven implemented planning generators share an explicit user-directive prompt contract.
It distinguishes user-directed draft content from source evidence and permits
unsupported preliminary additions without weakening artifact codecs, source
reference truthfulness, relation integrity, hierarchy, lifecycle or atomic
persistence. The shared wording is prompt guidance only; validation remains at
the existing artifact-specific boundaries.

The Navigator is a read-only advisory application capability over the existing
project, source and draft boundaries:

```text
Active canonical project root
        +
current normal document index
        +
project-bound DraftStore inventory
        +
implemented phase capabilities
        ↓
PlanningSnapshot
        ↓
deterministic eligibility, action, blockers and priority level
        ↓
validated candidates
        ↓
optional bounded AI ranking within each level
        ↓
at most three transient recommendations
```

The result is neither an `ArtifactDraft` nor a project artifact. The operation
does not write project files, change DraftStore, approve content, generate an
Office file or execute a recommended action.

### Separate normal-artifact and draft state

The snapshot keeps two independent dimensions:

```text
NormalArtifactState = ABSENT | UNIQUE | AMBIGUOUS | INVALID
DraftLifecycleState = MISSING | AI_DRAFT | APPROVED | INVALID
```

`APPROVED` belongs only to the existing draft lifecycle. It does not mean that
a normal project artifact exists and does not satisfy a soft predecessor
relationship without a separately resolved normal file.

`NormalArtifactResolver` starts from a freshly reloaded document
index for the explicit active canonical project root. A normal candidate must
be contained by that root, readable through the normal document boundary,
outside runtime folders and AI-Input, have the expected canonical
`document_type`, and have exactly one of these lifecycle combinations:

```text
STYRANDE / Gällande
PROJECT_ARTIFACT / Projektunderlag
```

The expected types are Riskanalys, Måldokument, Leveransdokument, WBS,
Tidplan, Intressentanalys and Kommunikationsplan. `STYRANDE/Gällande` has priority over
`PROJECT_ARTIFACT/Projektunderlag`. Exactly one readable and verifiable
candidate at the highest available level resolves to `UNIQUE`; multiple
candidates there resolve to `AMBIGUOUS`. If a candidate at that highest level
cannot be read or verified, resolution is `INVALID`. The resolver may not
ignore it and declare another file `UNIQUE`. Artifact-specific structural
verification is applied where the existing source boundary requires it.

Intressentanalys reuses the established XLSX title-grounding reader to verify
its ID/title structure. Kommunikationsplan verifies the three-sheet Office
contract and resolves every declared STK, TPG, selected activity/milestone and
grounded title against the current normal Intressentanalys and Tidplan. When a
current normal Tidplan has Huvudaktiviteter, their complete ordered ID set must
still be represented. A relation or ordering that no longer grounds makes the
normal Kommunikationsplan `INVALID`; the resolver never rewrites the workbook.

This resolution proves identity at the current source boundary, not semantic
quality or technical freshness. The first slice has no source fingerprints or
stable source versions and may not infer freshness from modification time.

### Deterministic candidates and hard priority levels

The deterministic policy owns candidate eligibility, action, blockers and
priority level. Its actions are:

```text
CREATE
REVIEW
INVESTIGATE
NO_ACTION
```

An `AI_DRAFT` always produces REVIEW when it can be shown safely. The core has
no reviewed state. After viewing, the user independently selects Task 1's
Revidera or Godkänn function. REFINE remains the internal planning operation
behind Revidera; UPDATE remains reserved for execution. The Navigator neither
selects nor recommends approval as its own action.

The four hard levels are:

1. existing work or identity problems — REVIEW for an `AI_DRAFT`, INVESTIGATE
   for invalid draft state or ambiguous/invalid normal resolution;
2. ready CREATE — a missing artifact with legitimate input whose soft
   predecessor exists as a `UNIQUE` normal artifact, Kommunikationsplan when at
   least one of Intressentanalys or Tidplan is `UNIQUE`, plus ready Mål,
   Riskanalys and Intressentanalys which have no predecessor;
3. permitted out-of-order CREATE — the candidate's own legitimate sources are
   sufficient but a soft predecessor is absent;
4. readiness investigation — relevant input is absent or readiness cannot be
   established safely.

Project-root, identity, DraftStore-binding or global phase inconsistency stops
the analysis before candidate ranking. If no level contains a candidate, the
result contains NO_ACTION.

`Mål → Leveranser → WBS → Tidplan` is a soft dependency chain and Riskanalys is
a cross-cutting track. Intressentanalys and Tidplan are the two strong soft
predecessors for Kommunikationsplan; neither is a hard requirement, and either
one may promote a CREATE candidate from level 3 to level 2. Within one level
the stable fallback/tie-break order is Mål, Riskanalys, Intressentanalys,
Leveranser, WBS, Tidplan, Kommunikationsplan. This order is not necessarily the
AI-enriched order inside that level.

### Bounded AI ranking

AI receives only validated candidates and a bounded deterministic projection
of their allowed legitimate sources. Each input candidate already has a
stable ID, artifact type, action, priority level, deterministic blockers and
allowlisted source references.

AI may return an ordering within each level plus grounded reasons, semantic
gaps, uncertainty and investigation questions. It cannot create or remove a
candidate, change its artifact, action or level, pass a blocker, rank across a
level boundary, introduce a source reference outside the candidate's allowed
set, or execute anything. AI observations are advisory and do not become
deterministic blockers.

The output schema must require known unique candidate IDs and a complete
permutation of the analyzed candidates inside each level. Candidate, schema,
level, action and source-reference validation is atomic: any invalid element
discards the entire AI ranking. The deterministic fallback order is then used
for every level. The same snapshot must always yield the same candidates,
actions, deterministic blockers, levels and fallback order; an accepted AI
ordering inside one level need not be bitwise deterministic.

Context projection must be stable, bounded and exactly testable without
loading `AI-utkast`, `Export` or another runtime area. Initial implementation
targets are at most 8,000 content characters per source and 32,000 in total.
These are implemented configuration values covered by automated tests and
revalidated in Task 5's representative demo QA, not permanent product constants.

### Public contracts

The implementation exposes presentation-independent immutable values
with the following conceptual responsibilities:

```text
PlanningSnapshot
├── canonical project root and directory identity
├── phase and implemented capabilities
├── normal-artifact states
├── DraftStore lifecycle states
├── legitimate source inventory
└── warnings

PlanningCandidate
├── stable candidate ID
├── artifact type
├── action
├── hard priority level
├── deterministic reason and blockers
└── allowed source references

PlanningRecommendation
├── candidate
├── advisory rank and reason
├── semantic gaps and uncertainty
└── investigation questions

PlanningNavigatorResult
├── at most three visible recommendations
├── notices and warnings
└── ai_enriched or deterministic_fallback mode
```

The corresponding application seams are `NormalArtifactResolver`,
`PlanningSnapshotProvider`, the deterministic eligibility/priority policy,
the semantic candidate ranker and `PlanningNavigatorService`. Their
responsibilities and separation may not be collapsed into Streamlit or an AI
prompt.

### Presentation and resume behavior

Streamlit or a future client may retain the current result set and transiently
skip a recommendation. Skipping does not modify the snapshot or candidate
policy and never triggers an action. The presentation may fill the vacated
place with the next candidate from the same already ranked result/snapshot so
that at most three non-skipped recommendations remain visible. A project
switch, explicit new analysis or relevant lifecycle change clears the
presentation state.

The Navigator's core does not require a review receipt. It collects or forwards
no Navigator-owned generic free text or approval comment. Skapa input goes only
to the already selected generator, Revidera input only to the already selected
revision, and Utred input only to an explicitly selected analysis. Visa and
Exportera have no generic input. After REVIEW, the user may choose the separate
existing Task 1 Godkänn action. Task 1's optional approval comment remains
available through `approve_draft(..., comment="")` and is not changed by the
Navigator contract. It is input to the already explicitly selected approval
action and can never affect routing, candidate selection, action assignment or
ranking. Task 1 execution remains separate and deterministic.

Export is outside Navigator actions and ordering. The UI may show the existing
human process notice for an approved draft without a normal artifact: export,
human review, placement of an accepted copy in `Projektplanering`, then an
explicit context reload. Export itself never changes the source snapshot.

The core imports no Streamlit types. A future Svelte/API client
must call the same application boundary rather than duplicate normal-artifact
resolution, eligibility, levels, ranking validation, source rules or
lifecycle logic.

### Failure contract

Global project isolation or phase failure stops the analysis. Artifact-level
ambiguity, unreadable highest-level candidates, invalid strict Sprint 7 draft
content and invalid draft state produce INVESTIGATE without automatic repair.
An ungroundable normal Kommunikationsplan relation is an artifact-level invalid
state, not a hidden REFINE or automatic propagation. A failed source read may be isolated
only when it cannot affect the highest-level resolution. AI timeout, schema
failure, unknown candidate ID, cross-level ordering, changed action or
unallowlisted source reference discards the entire AI result and selects the
deterministic fallback. A later failed Task 1 action leaves Navigator and
project state unchanged for the next explicit analysis.

---

# AI Architecture

The language model performs reasoning.

The application owns workflow and state.

```
User

↓

Gemini

↓

Tool selection

↓

Workflow

↓

Generator

↓

Human review

↓

Approval

↓

Export
```

The model is responsible for:

- understanding user intent
- generating draft content

The application is responsible for:

- state
- workflow
- approval
- persistence
- file generation

---

# Testing Strategy

Testing follows several levels.

## Unit tests

Verify individual components.

Examples:

- SourceSelector
- DraftStore
- Generator

---

## Workflow tests

Verify orchestration.

Examples:

- PlanningCreateRiskAnalysisWorkflow

---

## Integration tests

Verify multiple components together.

Example:

Risk Analysis end-to-end flow.

---

## Manual runtime verification

Verify the complete application using a real Gemini client.

## Native Office and visual verification

Automated renderer and template tests are necessary but do not prove that a
production Office document is visually usable. A new or materially changed
Office artifact is normally verified through this sequence:

```text
Automated tests
→ normal open in locally installed Microsoft Word or Excel
→ representative exported document
→ human visual and product review
```

Native Office verification uses a separate application instance, opens the
original read-only or works on a temporary copy, and closes without saving.
Warnings, repair requests, formula recalculation, layout, and template
immutability are checked as appropriate for the format.

---

# Current Limitations

Current architecture supports the completed Riskanalys, Tidplan, Mål,
Leveranser, WBS and Intressentanalys workflows, while its runtime draft state can hold several
artifact types.

Known future improvements include:

- Additional artifact workflows
- Broader lifecycle support beyond planning
- Explicit cross-artifact ID grounding where a normal selected source does not
  expose machine-verifiable internal identifiers
- Shared authoritative UU reference context for organization, responsibility,
  ownership, and service management
- A scenario-specific UPDATE path for conservative execution-phase revision
- Modernization of legacy/prototype tests and continued monitoring of repeated
  validation, canonicalization, persistence, and rollback ordering

These are planned improvements, not architectural defects.

The cross-artifact seam must not be bypassed through hidden reads from
`AI-utkast`, sidecars, a global ID index, a new persistent database, or by
treating `Export` as project source. The UU reference context and UPDATE path
are future architecture questions, not implemented capabilities. A likely
future UPDATE flow will need to evaluate `phase + operation` before scenario,
source selection, `ArtifactContext`, conservative revision, and traceable
change summary; its exact design remains undecided.

---

# Evolution Roadmap

Completed:

- Sprint 1 – Risk Analysis end-to-end lifecycle
- Sprint 2 – Stateful draft and Office rendering platform
- Sprint 3 – Tidplan as the second artifact, including CLI, Streamlit, Office
  export, Microsoft Excel verification, and sprint review
- Sprint 4 – Mål and Leveranser through the established artifact lifecycle,
  including strict Markdown contracts and Microsoft Word verification
- Sprint 5 – WBS in Excel and WBS–Tidplan interaction, including strict
  grounding, project isolation and Microsoft Excel verification
- Sprint 7 Task 4 – Intressentanalys through the shared lifecycle with strict
  provenance-preserving Markdown and a three-view XLSX presentation

Planned:

- Later – project creation and metadata editing, local demo UX improvements,
  and dual DOCX/XLSX Riskanalys export

---

# Related Documentation

| Document | Purpose |
|----------|---------|
| README.md | Project overview and getting started |
| AGENTS.md | Development guidelines |
| PRODUCT.md | Product principles and information authority |
| ADR.md | Architecture decisions |
| ROADMAP.md | Long-term planning |
| SPRINTS.md | Sprint planning and progress |
| IDEAS.md | Future ideas and technical debt |

---

## Runtime vs Project Data

The system distinguishes between project information and runtime state.

Project information represents the project itself and may be indexed and used as AI source material.

Runtime state represents the assistant's current work and must never be treated as project knowledge.

### Project information

Examples:

- Styrdokument
- Projektplanering
- Projektgenomförande
- Projektavslut
- Styrgrupp
- Beslut
- AI-Input

### Runtime state

Examples:

- AI-utkast
- Export
- AI-arbetsyta

Runtime folders are never indexed as project source material.

---

# Architecture Philosophy

This project deliberately grows through small, testable architectural slices.

When introducing new functionality:

1. Prefer extending existing architecture over creating parallel implementations.
2. Keep responsibilities explicit.
3. Preserve human review and approval.
4. Add tests before broad refactoring.
5. Document important architectural decisions in ADR.md.

Architecture should become simpler over time, not more complicated.

Application state is deterministic; AI behavior is probabilistic.

The LLM should be stateless. The application should be stateful.
