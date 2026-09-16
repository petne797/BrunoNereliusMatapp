# Architecture Decision Records


# Architecture decisions

Keep entries brief. Record decisions here only after they are accepted.

## ADR-001 - Optimize for a vertical MLP

**Status:** Accepted
**Date:** 2026-08-25

**Decision:** Stabilize one complete artifact lifecycle and reuse it for risk analysis, WBS, and timeline before expanding artifact coverage.

**Reason:** The registry already describes many artifacts, but the current runtime paths are incomplete and inconsistent. Three complete demo flows provide more value than additional disconnected definitions.

**Consequences:** Work is prioritized by complete user journeys rather than by the number of configured artifacts. Additional artifact coverage waits until the shared lifecycle is stable.

## ADR-002 - Keep human approval in the output path

**Status:** Accepted
**Date:** 2026-08-25

**Decision:** Generated files require the sequence draft, review or revision, explicit approval, then generation.

**Reason:** This matches the project-assistant role and prevents AI-generated working material from being presented as an approved project decision.

**Consequences:** Document-generation paths must check approval state. Creating or revising a draft must not write a final output file.

## ADR-003 - Use the existing document models by lifecycle stage

**Status:** Accepted
**Date:** 2026-08-25

**Decision:** Use `ProjectDocument` for source material, `ArtifactDraft` for editable generated content, and `GeneratedDocument` at the file-output boundary.

**Reason:** A single representation at each stage removes the current draft contract mismatch and keeps source, draft, and output responsibilities visible.

**Consequences:** Runtime components must convert explicitly between lifecycle stages. `DraftStore` stores `ArtifactDraft`, while file writers receive `GeneratedDocument`.

## ADR-004 - Improve the transitional architecture incrementally

**Status:** Accepted
**Date:** 2026-08-25

**Decision:** Connect or consolidate the live CLI and artifact paths in small verified steps. Do not introduce a third path or perform a broad rewrite for Sprint 1.

**Reason:** Incremental changes support the learning goal and reduce risk to the demo.

**Consequences:** Each architectural change should be small, independently reviewable, and covered by focused tests. Legacy code is removed only after its replacement is connected and verified.

## ADR-005 - Public tool functions remain stable adapters

**Status:** Accepted
**Date:** 2026-08-25

**Decision:** New functionality is implemented behind workflows rather than by extending legacy implementations.

**Reason:** Stable tool entry points preserve the Gemini tool contract while workflows provide the intended home for new artifact behaviour.

**Consequences:** Public tool functions remain thin adapters. Artifact selection, source handling, and generation logic belong behind the workflow boundary.

## ADR-006 - Let PlanningCreateRiskAnalysisWorkflow own orchestration

**Status:** Accepted
**Date:** 2026-08-25

**Decision:** `PlanningCreateRiskAnalysisWorkflow` owns the risk-analysis creation flow from source selection and `ArtifactContext` construction through generation of an `ArtifactDraft`.

**Reason:** Keeping orchestration in one workflow makes the runtime path explicit and allows the public tool function to remain a thin adapter.

**Consequences:** `create_risk_analysis()` delegates to the workflow and returns its result unchanged. Generation, state, and presentation must not be coordinated by the adapter.

## ADR-007 - Use GeneratedDocument as the output boundary

**Status:** Accepted
**Date:** 2026-08-25

**Decision:** Convert an approved `ArtifactDraft` to `GeneratedDocument` before generating an output file.

**Reason:** A dedicated output model separates editable draft state from file-ready content and makes the transfer of project, artifact, status, and source metadata explicit.

**Consequences:** File generators receive `GeneratedDocument`, not `ArtifactDraft`. Conversion requires an approved draft and must not alter the draft or implicitly perform approval.

## ADR-008 - Provide runtime state explicitly before tool selection

**Status:** Accepted
**Date:** 2026-08-25

**Decision:** The application owns workflow state. Before every Gemini request, a deterministic snapshot of the current `DraftStore` state is added to the request.

**Reason:** Gemini needs the current draft type and status to select the correct tool, while workflow state must remain explicit and controlled by the application.

**Consequences:**

- The LLM remains stateless.
- The application owns state.
- Tool contracts remain unchanged.
- No conversation memory is required.

## ADR-009 - ArtifactDraft identity and multi-draft state

**Status:** Accepted
**Date:** 2026-08-26

**Decision:** Use `artifact_type` as the stable identity of an `ArtifactDraft`. `DraftStore` owns a mapping of drafts keyed by artifact type, and public draft operations select a draft by that identity.

**Reason:** A project may contain several simultaneous AI working drafts. A single current-draft value cannot represent that state safely and makes revision, approval, and export ambiguous.

**Consequences:** All valid Markdown drafts are restored into runtime state. The runtime snapshot lists artifact type and status for every draft. When several drafts exist and the user has not identified one, the assistant asks for clarification instead of guessing. `ArtifactDraft.status` is the single source of truth for draft status.

## ADR-010 - Use ordinary Office documents as templates

**Status:** Accepted
**Date:** 2026-08-26

**Decision:** Office templates are ordinary `.docx` and `.xlsx` files. `OfficeTemplate` is format-neutral, `ArtifactDefinition` selects the template, and `GeneratedDocument` carries the resolved template to the renderer. Renderer selection and output extension are based on the template file extension. `GeneratedDocument.content` remains the approved textual representation, while its optional read-only `structured_content` mapping carries typed domain data reconstructed at the export boundary. An Office renderer uses the explicit representation required by the artifact presentation. `ArtifactDraft` remains presentation-independent.

**Reason:** The supported Python libraries can open, modify, and save ordinary Office documents while preserving their formatting and static content. True `.dotx` files are not reliably supported by the current `python-docx` version. Keeping template selection in the artifact definition avoids hardcoded template knowledge in file generators.

**Consequences:** Artifact definitions may declare `office_template.path` and optional renderer configuration. The loader accepts `.docx` and `.xlsx`, validates that the file exists, and rejects unsupported formats clearly. Office rendering is format-specific: the shared architecture standardizes template selection and export orchestration but deliberately does not require Word and Excel to use identical rendering techniques. `GeneratedDocument` may carry textual content, structured content, or both depending on the renderer. Text-oriented Word artifacts consume `content`; structured Word presentations and spreadsheets may consume explicit typed `structured_content`. Renderers must not reconstruct domain data by interpreting arbitrary Markdown, JSON, or prose. `structured_content` is transient and is never persisted in `ArtifactDraft` or beside its canonical Markdown. `ArtifactDraft` and Markdown persistence are unchanged.

## ADR-011 - Domain model owns meaning, renderers own presentation

**Status:** Accepted
**Date:** 2026-08-26

**Decision:** Structured artifacts expose domain concepts rather than spreadsheet concepts. For example, Tidplan exposes `ScheduledActivity` objects through `activities` instead of exposing spreadsheet rows.

**Reason:** Domain data should describe project meaning independently of the Office layout used to present it.

**Consequences:** Word and Excel renderers remain presentation-specific. Future renderers may transform the same domain object into different Office layouts without changing artifact generation.

## ADR-012 - Strict Markdown is the persistent source of truth for structured drafts

**Status:** Accepted
**Date:** 2026-08-27

**Decision:** `ArtifactDraft.content` remains text. A structured artifact may define a strict, visible, and deterministically validated text contract. Tidplan uses exactly one Markdown file with a YAML block containing `activities` and `open_questions`; this is its sole persistent representation. The content is parsed into validated `ScheduledActivity` values and serialized back to canonical Markdown before it is saved after generation or revision.

**Reason:** The draft must remain human-readable and editable while preserving typed domain meaning through revision, persistence, restore, and approval. A strict text contract provides deterministic reconstruction without adding structured state to `ArtifactDraft` or creating a second persistent source of truth.

**Consequences:** Tidplan generation and revision must validate before changing DraftStore or `AI-utkast/Tidplan.md`. Restore validates without rewriting and skips invalid or older free-form Tidplan drafts. Approval requires at least one valid activity and no unresolved `open_questions`. At the export boundary, the same codec reconstructs `GeneratedDocument.structured_content["activities"]` as an ordered tuple of validated `ScheduledActivity` values while preserving the approved Markdown unchanged in `GeneratedDocument.content`. Risk Analysis and other free-text artifacts keep their existing behaviour.

**Rejected alternatives:** Parsing arbitrary Markdown is ambiguous. Storing structured data in both `ArtifactDraft` and Markdown, or in a sidecar file, risks divergence. Letting the Excel renderer parse draft text mixes domain and presentation concerns. A new Gemini interpretation during export would make approved content nondeterministic.

## ADR-013 - Use artifact-specific allowlisted Excel renderers when presentation semantics differ

**Status:** Accepted
**Date:** 2026-08-29

**Decision:** Keep `OfficeTemplate` and `OfficeDocumentGenerator` as shared
Office boundaries. Do not turn the Tidplan-specific `ExcelTemplateRenderer`
into a general WBS renderer through special cases. A future WBS Excel renderer
will instead be selected by a closed, allowlisted renderer identifier in
`OfficeTemplate.renderer_config`. Configuration may not supply an import path
or callable, and unknown identifiers must fail clearly.

**Reason:** Tidplan renders scheduled activities, rows, timelines and Gantt
semantics. WBS renders a hierarchy and presentation projection with different
semantics. Forcing both into one renderer would couple the WBS domain to the
Tidplan template contract, while a general Excel/table engine would be
premature before several concrete renderers expose a stable common seam.

**Consequences:** WBS can receive a focused Excel renderer while template
selection, versioned output, approval boundaries and transient structured
content remain shared. Existing Tidplan rendering remains unchanged. A small
shared helper may be extracted only when future implementations demonstrate a
real common seam; no generic renderer framework is introduced now.

## ADR-014 - Bind runtime state to the selected canonical project root

**Status:** Accepted
**Date:** 2026-08-31

**Decision:** The canonically resolved selected project directory is the
runtime root, and its directory name is the project identity. `metadata.json`
is read only from that already selected root, and its `name` field is
deprecated legacy metadata ignored by runtime. DraftStore, restore and the
document index are bound to the same root and directory identity;
model-provided project names can only confirm the active identity and cannot
select or change it.

**Reason:** Directory selection, metadata display names, persisted draft
metadata and global runtime objects previously had no single enforced
identity contract. A metadata mismatch could produce the wrong displayed
project name, while stale or tampered state was not rejected consistently at
all lifecycle boundaries.

**Consequences:** A differing `metadata.name` neither blocks activation nor
changes runtime identity. Project switches replace project-bound draft and
source state. Restore, CREATE, REFINE, approval and export still reject root,
directory-identity and stale-state mismatches before changing DraftStore,
Markdown, status or output. Existing metadata is never silently normalized,
and no new multitenant or general file-access layer is introduced. Removing
existing legacy fields is a separate migration; no project-creation flow
exists in the current runtime to update. Renaming is also separate work and
requires a controlled flow, preferably with a stable `project_id` introduced
before storage location and display name can be decoupled.

## ADR-015 - Use deterministic Navigator eligibility with bounded AI ranking

**Status:** Accepted
**Date:** 2026-09-02
**Extended:** 2026-09-15 for Sprint 7's two additional artifacts

**Decision:** The first Planning Navigator is an advisory hybrid, not a new
artifact lifecycle or a linear wizard. A deterministic application policy
constructs the complete candidate set for Riskanalys, Mål, Leveranser, WBS and
Tidplan, Intressentanalys and Kommunikationsplan and assigns each candidate its
action, deterministic blockers, one of four hard priority levels, and a stable
fallback position. AI may only rank
candidates within the same priority level and add grounded reasons, semantic
gaps, uncertainties and investigation questions.

The deterministic levels are: existing work or identity problems; ready
CREATE whose soft predecessors exist as unique normal project artifacts;
permitted out-of-order CREATE; and INVESTIGATE for insufficient readiness.
`Mål → Leveranser → WBS → Tidplan` remains a soft relation, Riskanalys is a
cross-cutting track, and Intressentanalys plus Tidplan are Kommunikationsplan's
two strong soft predecessors. Neither is a hard requirement; either `UNIQUE`
normal predecessor promotes a ready Kommunikationsplan CREATE from level 3 to
level 2. The fallback/tie-break order within a level is Mål, Riskanalys,
Intressentanalys, Leveranser, WBS, Tidplan, Kommunikationsplan. AI cannot create or remove a candidate,
change action or level, pass a blocker, rank across a level boundary, or
execute anything. The entire AI result is discarded when schema, candidate,
level, action or source-reference validation fails.

Normal project-artifact state and DraftStore lifecycle state remain separate.
A normal artifact is resolved only from the active canonical project root and
current document index with the exact lifecycle combinations
`STYRANDE/Gällande` or `PROJECT_ARTIFACT/Projektunderlag`; the former has
priority. Exactly one readable and verifiable candidate at the highest
available level is `UNIQUE`. Multiple candidates are `AMBIGUOUS`. An unreadable
or unverifiable candidate at that highest level makes the result `INVALID` and
cannot be ignored in favor of a lower or other candidate. Both ambiguous and
invalid resolution require INVESTIGATE. An `APPROVED` draft without a normal
project artifact does not satisfy a soft predecessor relationship.

For the structured Sprint 7 XLSX artifacts, normal resolution also reuses the
existing Office grounding boundaries. Kommunikationsplan relations and the
ordered Huvudaktivitet coverage are checked against the current unique normal
Intressentanalys and Tidplan. A relation that no longer grounds is `INVALID`
and yields INVESTIGATE after context reload; it never triggers automatic
mutation. This is relation validation, not a general freshness/version model.

The Navigator actions are CREATE, REVIEW, INVESTIGATE and NO_ACTION. An
`AI_DRAFT` always yields REVIEW; after viewing, the user independently chooses
the existing Revidera or Godkänn action. No new reviewed state is introduced.
REFINE remains the internal planning operation behind the user-facing term
Revidera, while UPDATE remains reserved for execution. Export remains outside
Navigator steps and ordering. Transient presentation-only skipping may fill
the visible list from later candidates in the same snapshot, up to three
visible recommendations, but cannot mutate core state or execute an action.

**Reason:** A purely sequential guide would contradict the accepted iterative
planning model. Free AI routing would weaken Task 1's deterministic behavior,
allow hidden action selection and make fallback unreliable. A deterministic
candidate policy preserves lifecycle, source authority, project isolation and
human control while bounded AI ranking provides useful semantic advice where
several actions are equally eligible.

**Consequences:** The Navigator result is transient and never becomes an
`ArtifactDraft`, project artifact or persistent source of truth. Identical
snapshots always produce identical candidates, actions, deterministic
blockers, priority levels and fallback order; AI-enriched ordering inside one
level need not be bitwise deterministic. AI output must be schema- and
candidate-validated as a whole. `AI-utkast`, `Export` and other runtime areas
are never project sources. Streamlit may retain only presentation state; the
core must be usable behind a future API/Svelte client. Context projection must
be bounded, stable and testable. Initial implementation targets of 8,000
characters per source and 32,000 total are configuration values to verify,
not permanent product rules. This ADR records the Navigator contract; its
implementation is described in `ARCHITECTURE.md`.

**Rejected alternatives:** A mandatory linear sequence would turn soft
dependencies into false prerequisites. A fully AI-selected next action would
reintroduce probabilistic hidden routing. Persisting Navigator position,
reviewed flags or skipped artifacts would create a competing workflow state.
Treating approved drafts or Export as normal project information would bypass
the existing human acceptance and source boundaries.

## ADR-016 - Reuse the existing lifecycle for accepted planning iterations

**Status:** Accepted (2026-09-10)

**Decision:** Planning supports two explicit iteration paths through the
existing artifact lifecycle. Baseline REFINE requires exactly one readable and
verifiable `UNIQUE` normal artifact. Clean-slate CREATE excludes the previous
draft and normal versions of the same artifact type. Both paths use current
legitimate sources, including explicit `AI-Input`, according to the existing
source-selection rules and produce a new `AI_DRAFT`.

The existing normal artifact is never replaced automatically. An export is not
a normal project source until the Product Owner has reviewed it and placed an
accepted copy in `Projektplanering`. Freshness/provenance and automatic normal
artifact replacement remain out of scope.

**Reason:** This provides a second planning iteration while preserving the
human-in-the-loop lifecycle, source authority, project isolation and the
existing DraftStore/persistence boundaries.

## ADR-017 - Treat explicit project-manager instructions as draft directives

**Status:** Accepted (2026-09-10)

**Decision:** For the seven implemented planning artifacts, an explicit functional
instruction may add, change or remove preliminary draft content without source
support. It is authoritative for requested draft content, not evidence for
project facts. Missing optional relations and values use each artifact's empty,
null or question representation. Source references remain truthful and all
codec, project, ID, hierarchy, lifecycle and persistence invariants remain
absolute.

**Consequences:** The shared prompt contract is reused by all seven generators.
The result remains `AI_DRAFT` and requires human review and approval/export;
the instruction cannot create accepted project knowledge or bypass validation.

## ADR-018 - Treat planning folders as broad source domains

**Status:** Accepted
**Date:** 2026-09-11

**Decision:** During Planering, all readable documents under
`Styrdokument/**` and `Projektplanering/**` are legitimate project sources.
This applies regardless of whether a document was produced by
Projektassistenten, written manually or created by another tool.
`Projektplanering/AI-Input/**` is included as part of that source domain and
retains its role as an explicit place for additional working material.
`AI-utkast/**` and `Export/**` remain runtime/output and are excluded.

Placement in `Styrdokument/**` means that the document is current, established
and governing. Only the currently applicable version of a governing document
should be kept in that active source domain; history belongs in SharePoint
version history or elsewhere. When both a governing Projectplan and
Projektdirektiv exist, Projectplan is the primary description of the current
plan, while Projektdirektiv remains governing background for mandate, purpose
and information not superseded by Projectplan.

Artifact-specific source rules may rank relevance but must not turn known
artifact/document types into an exclusive allowlist for `Projektplanering`.
The operation-specific rules from ADR-016 remain: clean-slate CREATE may
exclude prior drafts and normal versions of the same artifact type, while
baseline REFINE is based on one unique accepted normal artifact.

**Reason:** The project folder structure is intended to communicate
information role to both the project manager and the assistant. Project
managers may legitimately create planning documents manually, and forcing all
such material through `AI-Input` or a registered artifact type would make the
source model less transparent and less useful. Governing documents need a
stronger and simpler contract: if a file is in `Styrdokument`, it is expected
to be current and authoritative.

**Consequences:** The shared planning SourceSelector/source definitions must
be adjusted so the rule applies consistently to the five existing planning
artifacts and new Sprint 7 artifacts. Source inclusion and source authority
remain separate: lower-authority planning material cannot silently overwrite
contradictory governing information. This ADR changes the source-eligibility
aspect of ADR-016 but does not change its lifecycle semantics. Execution-phase
UPDATE and status-report source selection remain separate future discovery.

## ADR-019 - Add schedule groups as Tidplan's shared high-level time axis

**Status:** Accepted
**Date:** 2026-09-11

**Decision:** Tidplan gains a simple presentation-independent `ScheduleGroup`
concept, shown to users as **Huvudaktivitet**. A Huvudaktivitet has a stable
identity such as `TPG-001`, a title, optional short description and order. A
`ScheduledActivity` may belong to at most one Huvudaktivitet by stable ID. The
Huvudaktivitet's date range is normally derived from its member activities and
is not a second persistent date source.

Schedule groups are an orientation/time-axis concept, not a WBS hierarchy.
WBS may inform the grouping but no new hard WBS→ScheduleGroup relation is
introduced in Sprint 7. Existing valid Tidplan content without groups remains
supported.

**Reason:** Kommunikationsplan needs a recognisable high-level project time
axis that stays pedagogically aligned with Tidplan. Adding a small grouping
concept fills an already deferred scheduling taxonomy need without turning
Tidplan into a duplicate work-breakdown structure.

**Consequences:** Tidplan's domain, strict codec, generator, restore/refine,
XLSX presentation and tests must be extended backwards-compatibly. A
structured normal Tidplan may then expose exact Huvudaktivitet IDs and titles
to other artifacts through a transient verified grounding boundary.

## ADR-020 - Model Kommunikationsplan as a strategic structured XLSX artifact

**Status:** Accepted
**Date:** 2026-09-11

**Decision:** Kommunikationsplan is a structured strategic planning artifact,
not a separate operational list of individual communication activities. Its
core combines target-group strategy with a matrix describing what each target
group should `Veta/Förstå`, `Känna/Inställning`, `Göra/Agera` and receive as
`Kärnbudskap` across communication-relevant project stages.

Target groups have stable identities such as `KMG-001` and may reference 0..N
verified `STK-...` identities from a normal Intressentanalys. When a unique
structured normal Tidplan with Huvudaktiviteter exists, all Huvudaktiviteter
normally appear in the same order as the matrix's main columns, including
stages where no special communication is planned. Selected communication-
critical Tidplan activities or milestones may supplement that axis. Stable IDs
own relationships; the XLSX presentation must also show verified human-readable
titles and never degrade to ID-only headings.

Intressentanalys and Tidplan are strong soft sources/predecessors, not hard
requirements. Kommunikationsplan may still be created from other legitimate
planning sources when either is absent. Changes to those source artifacts do
not automatically mutate Kommunikationsplan; advisory REFINE is the intended
follow-up mechanism.

The Sprint 7 Office contract is one XLSX presentation of the same canonical
artifact, with at least `Övergripande strategi`, `Målgruppsstrategi` and
`Kommunikationsmatris`. A separate Word rendering can be considered later but
is not a second persistent truth and is out of Sprint 7.

**Reason:** The supplied spreadsheet example demonstrates the pedagogical
value of aligning target groups with recognisable project stages, while the
PPS communication-plan template contributes useful strategic concepts such as
purpose, target-group goals, messages and channels. Keeping individual
communication actions in WBS/Tidplan or the project's operational work model
prevents duplicate planning state.

**Consequences:** Kommunikationsplan needs its own typed domain, strict
canonical draft codec, verified cross-artifact grounding and artifact-specific
XLSX renderer. It must reuse the shared lifecycle, source authority,
project-isolation, approval and export boundaries. No automatic cross-artifact
propagation or parallel communication-activity schedule is introduced.

## ADR-021 - Separate approval blockers from quality warnings

**Status:** Accepted
**Date:** 2026-09-14

**Decision:** Projektassistentens approvalmodell skiljer mellan tre nivåer:

1. **Hard validation / blocker** – tekniska eller semantiska fel som gör
   artefakten ogiltig, exempelvis fel canonical schema, ogiltiga enumvärden eller
   ID-format, verifieringsbara relationer som inte kan groundas,
   project-isolation-fel, korrupt exportunderlag eller brutna absoluta
   domäninvariants. Dessa fel blockerar approval och export.
2. **Quality warning** – öppna frågor, UNKNOWN-värden, tillåtna null-värden,
   preliminära antaganden, ofullständig analys och låg informationsmognad.
   Dessa ska synliggöras tydligt men blockerar normalt inte approval.
3. **Product recommendation** – rådgivande förslag, exempelvis att komplettera
   frågor, analysera fler intressenter eller förbättra svag täckning. Dessa
   blockerar inte approval.

Explicit human approval är readiness-gränsen när inga hard validation-fel
finns. Olösta `open_questions` ska därför inte generellt blockera approval.
Approval betyder godkänd för export efter mänsklig granskning; det betyder
inte att artefakten blir accepterad projektkunskap. Human review, export och
aktiv placering i `Projektplanering` gäller fortsatt.

Grounding, strict schema, project identity/isolation och andra absoluta
invariants förblir fail-closed. Artefaktspecifika regler får därför fortfarande
blockera när de skyddar faktisk dataintegritet eller semantisk giltighet.

ADR-012:s specifika formulering att Tidplan approval kräver “no unresolved
open_questions” är superseded av detta beslut. ADR-012:s övriga
strict-codec- och persistencebeslut förblir giltiga.

Den nuvarande implementationen är ännu inte harmoniserad med denna målbild.
Implementationen ska göras som en separat tvärgående refaktorering.

## ADR-022 - Treat Projektplan as a governed planning synthesis

**Status:** Accepted
**Date:** 2026-09-15

**Decision:** Projektplan is the planning phase's coherent final artifact. Once
accepted and placed in `Styrdokument/`, it is a governing document. It
summarizes and references the seven specialized planning artifacts while they
retain their domain ownership. Their absence is normally a quality warning,
open question or recommendation, not a CREATE or approval blocker.

During Planering, a Projektplan does not lock special artifacts: REFINE may
surface a conflict but neither blocks normally nor automatically mutates the
Projektplan. Projektplan REFINE starts from its current baseline. Information
that appears to change governing content is presented as old and proposed
information for explicit human decision. During Genomförande, established
governing documents form the baseline for a future conservative, traceable
UPDATE; that implementation is outside Sprint 8. `phase + operation`, rather
than document presence alone, controls behavior.

**Consequences:** Sprint 8 reuses the existing lifecycle and broad planning
source domains, with a Projektplan-specific canonical contract and no second
source of truth in the Word template. The BP4 template is a presentation and
quality reference, not a domain model. Navigator readiness is soft. Existing
ADR-016, ADR-018 and ADR-021 remain applicable and are not duplicated.

Projektplan's canonical contract is strict sectioned Markdown in
`ArtifactDraft.content`: an exact `# Projektplan` title and one ordered instance
of each defined H2 section. A small Projektplan-specific codec validates and
deterministically serializes that skeleton through generation, update, restore,
persistence and approval. It deliberately does not create a YAML domain model,
duplicate any specialised artifact domain, or make DOCX a second truth source.
The eighth Navigator candidate remains Task 4 scope.
