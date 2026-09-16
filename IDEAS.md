# Ideas

Ideas are not commitments. Promote an item to `ROADMAP.md` or `SPRINTS.md`
only when it is prioritized. Sprint 5 WBS discovery and WBS–Tidplan work are
already prioritized there and are therefore not duplicated here.

## Product needs not yet scheduled

- Add remaining artifact workflows one complete vertical slice at a time.
- Create projects and edit project metadata through supported application
  boundaries.
- Export the same approved Riskanalys to both DOCX and XLSX.
- Improve the local demo's UX, layout, navigation, and feedback.
- Evaluate a Svelte frontend and explicit Python API boundary when broader UI
  work is prioritized; do not duplicate state or domain logic in the client.

### Gemensam kvalitets- och godkännandemodell för planeringsartefakter

Ett framtida arbete ska inventera approval-reglerna för Riskanalys, Mål,
Leveranser, WBS, Tidplan, Intressentanalys och Kommunikationsplan och
klassificera dem som hard error, warning eller recommendation. Öppna frågor ska
inte vara en generell blockerare. UI-meddelanden ska harmoniseras och
artefaktspecifika override-lösningar förenklas eller ersättas där den
gemensamma principen gör dem överflödiga.

Fail-closed ska behållas för grounding, schema, identity/project isolation och
verkliga domäninvariants. Varje artefakt ska verifieras med fokuserade tester
och supported regression. WBS override-flödet bör omprövas i detta arbete,
men exakt implementation beslutas först då. Detta är ännu inte en prioriterad
sprint eller task och ska därför inte flyttas till `SPRINTS.md` eller
`ROADMAP.md`.

### Explicit språkval för Office-presentationer

Ett framtida UI-/produktarbete kan införa ett explicit språkval per projekt
eller export för svenska och engelska Office-presentationer. Valet ska styra
användarvända rubriker och presentationsvärden utan att ändra interna
domänvärden, enum-kontrakt eller canonical Markdown/YAML. Automatisk
språkdetektering och parallella persistenta språkversioner ingår inte i den
nuvarande artefaktarkitekturen och ska inte införas utan separat discovery.

### Deterministiskt kreativitetsval för planeringsartefakter

Som en möjlig del av en framtida samlad UI-översyn och ett eventuellt byte av
webbramverk kan projektledaren få välja en explicit kreativitetsnivå för CREATE
och REFINE, exempelvis **Källnära**, **Balanserad** och **Utforskande**. Valet
ska deterministiskt översättas till ett avgränsat promptkontrakt, medan den
befintliga funktionsspecifika fritexten fortsatt används för ämne, fokus och
kompletterande önskemål. Funktionen ska utvärderas tillsammans med den nya
interaktionsdesignen och behöver inte införas separat i nuvarande Streamlit-UI.

- **Källnära:** använd endast uppgifter som uttryckligen anges eller direkt
  stöds av legitima valda källor; informationsluckor blir öppna frågor.
- **Balanserad:** kombinera källgrundad information med tydligt identifierade
  professionella AI-förslag och materiella antaganden. Detta är tänkt
  standardläge.
- **Utforskande:** tillför fler relevanta planeringshypoteser och alternativ,
  fortsatt tydligt skilda från projektfakta och beslut.

Kreativitetsnivån får aldrig åsidosätta source authority, sanningsenliga
källreferenser, styrande beslut, domänvalidering, artifact grounding,
human-in-the-loop eller approval. Fritext och kreativitetsval är instruktioner,
inte källbevis.

Ett deterministiskt UI-val ger en konsekvent instruktion men garanterar inte i
sig modellens innehåll. Om produkten behöver en hård garanti för exempelvis
Källnära måste detta kompletteras med artefaktspecifik representation och
validering utan att omotiverat införa ett generellt persistent `origin`- eller
`AI_PROPOSAL`-fält. Idén flyttas till `ROADMAP.md` och därefter en sprint först
när behovet har validerats genom faktisk användning av Task 4A:s nuvarande
fritextstyrning och den framtida UI-/webbramverksöversynen prioriteras.

### Synkroniserad projektrot via OneDrive

Efter den framtida UI-/webbramverksöversynen ska en avgränsad pilot utvärdera
en gemensam projektyta som synkroniseras lokalt med OneDrive. Koden ska fortsatt
ligga separat och delas via Git/GitHub, medan varje användares lokala
Projektassistent konfigureras mot den egna lokala sökvägen till samma delade
projektyta. Ingen användarspecifik absolut sökväg får hårdkodas. Streamlit har
redan stöd för `PROJECT_ASSISTANT_PROJECTS_ROOT`; piloten ska bedöma hur samma
konfigurerade projektrot ska gälla konsekvent för alla relevanta startvägar och
det framtida API:t.

Syftet är att kollegor ska kunna lägga till projektunderlag och ta del av
accepterade artefakter utan att projektfiler läggs i kodrepot. Piloten ska göras
med ett syntetiskt projekt genom kopiering, inte genom att först flytta skarpa
projekt. Den ska genomföras före utrullning till kollegor eller en skarp
migrering.

Följande behöver fastställas och verifieras i piloten:

- vilken delad OneDrive- eller SharePoint-synkad yta som har lämpligt gemensamt
  ägarskap, behörigheter och informationsklassning;
- att projektfilerna är lokalt tillgängliga och inte endast online när
  Projektassistenten läser eller skriver dem;
- hur `AI-utkast` och `Export` ska delas eller hållas lokala utan att de blir
  projektkällor eller en andra sanningskälla;
- att secrets, loggar, cache och annan maskinlokal runtime-data hålls utanför
  den synkroniserade projektytan;
- hur synkfördröjning, oläsbara placeholders, långa Windows-/Office-sökvägar,
  Office-låsfiler och OneDrive-konfliktkopior ska upptäckas och presenteras;
- hur skrivningar till utkast och versionsnumrerad export skyddas mot att två
  lokala instanser samtidigt väljer samma fil eller skriver över varandra;
- att canonical project root, exakt projektidentitet, source selection,
  DraftStore-bindning, approval, export och projektisolering fungerar med en
  projektrot utanför kodrepot.

Olika projekt per projektledare minskar konfliktrisken men skapar ingen
transaktionell fleranvändarsäkerhet. Den första piloten kan därför använda
regeln **en aktiv skrivande projektledare per projekt**. Samtidigt arbete i
samma projekt eller artefakt kräver ett separat beslut om konfliktvarning,
ägarskap eller låsning; OneDrive-synk ska inte behandlas som en databas eller
som ett distribuerat lås. Lokalt atomiska filskrivningar är inte i sig
atomiska mellan flera synkroniserade datorer.

Detta är en möjlig mellanlösning före en bredare SharePoint/M365-arkitektur.
Piloten innebär inte att SharePoint, Graph, Entra, Azure-hosting eller en
produktionsmässig fleranvändartjänst har valts eller implementerats.

## Prioritized in Sprint 6

The active scope, task order, acceptance criteria, and Definition of Done live
in `SPRINTS.md`. The sections below retain the design background that led to
the prioritization; they are not a separate implementation contract.

### Planeringsnavigator / Rekommendera nästa steg

Task 4B's baseline-REFINE and clean-slate-CREATE paths are now implemented
through the existing lifecycle. They do not establish freshness, provenance,
or a second persistent source of truth; those remain later design questions.

Den första rådgivande Planeringsnavigatorn hjälper projektledaren att bedöma
vilka av de fem implementerade
planeringsartefakterna som bör skapas, granskas eller förfinas härnäst.
`PRODUCT.md`, `ADR.md` och `ARCHITECTURE.md` beskriver det beslutade kontraktet
och den implementerade arkitekturen. `SPRINTS.md` äger aktuell implementation-
och QA-status; texten här bevarar endast bakgrund och senare möjliga
utbyggnader.

Den första slicen analyserar:

- aktuellt Projektdirektiv och andra styrande källor;
- accepterade dokument i `Projektplanering`;
- underlag som projektledaren uttryckligen har lagt i `AI-Input`;
- vilka artefakter som redan finns;
- projektfas och tillåtna operationer;
- förändringar och semantiska gap mellan relaterade artefakter.

Resultatet kan rekommendera CREATE för en saknad artefakt, REVIEW för ett
`AI_DRAFT`, INVESTIGATE vid underlags- eller identitetsproblem och NO_ACTION
när inget körbart nästa steg finns. REFINE är det interna planeringsbegreppet
bakom den separata användarfunktionen Revidera; genomförandefasens UPDATE ingår
inte. Export är inte en Navigatoraction.

#### Human-in-the-loop and legitimate sources

`AI-utkast` och `Export` får aldrig användas som projektkällor. En exporterad
artefakt blir inte automatiskt accepterad projektkunskap. Projektledaren ska
först granska den exporterade filen och aktivt placera den accepterade
versionen i `Projektplanering`. Först därefter får Planeringsnavigatorn eller
en efterföljande artefakt använda innehållet enligt respektive artefakts
source-selection-regler. `Styrdokument`, `Projektplanering` och uttryckligt
tillfört `AI-Input` är legitima källklasser inom dessa regler.

Det avsedda arbetsflödet är:

```text
Skapa utkast
→ projektledaren granskar
→ godkänn och exportera
→ projektledaren granskar den exporterade filen
→ accepterad version placeras i Projektplanering
→ projektkontexten laddas om
→ Planeringsnavigatorn analyserar nästa steg
```

Efter export får agenten ge en processuppmaning om att granska dokumentet och
placera en accepterad version i `Projektplanering`. Exportens innehåll får
däremot inte användas för att rekommendera nästa artefakt. Navigatorn förblir
rådgivande och får inte automatiskt skapa, uppdatera, godkänna eller exportera
artefakter. Projektledaren väljer uttryckligen om en rekommenderad CREATE- eller
REFINE-operation ska startas.

#### Actuality and semantic gaps

Det godkända första kontraktet skiljer mellan:

1. **Teknisk aktualitet:** en legitim källa har tillkommit eller förändrats,
   eller artefakten togs fram från andra eller äldre källversioner.
2. **Semantisk aktualitet:** relaterade artefakter innehåller motsägelser,
   luckor eller nya beroenden vars faktiska betydelse behöver AI-analys.

Första slicen identifierar normala projektartefakter deterministiskt men gör
inga påståenden om teknisk freshness. En senare iteration ska införa en
strukturerad freshness- och proveniensmodell som kombinerar:

- tidpunkt för när artefaktens innehåll skapades eller reviderades;
- tidpunkt för mänskligt godkännande;
- tidpunkt för export;
- stabil innehållsversion och innehållsfingeravtryck;
- spårbara `baserades på`-relationer till de käll- och artefaktversioner som
  användes när innehållet togs fram.

Exporttid är spårbarhetsinformation och får inte ensam avgöra aktualitet,
eftersom oförändrat äldre innehåll kan exporteras på nytt. En
`baserades på`-relation ska göra det möjligt att deterministiskt upptäcka att
exempelvis en Tidplan bygger på en äldre WBS-version. Tidsstämplar och
proveniens ger beslutsunderlag, men de avgör inte ensamma vilken motstridig
uppgift som är sann. Semantiska konflikter ska fortsatt synliggöras för
mänsklig bedömning, och en konflikt som inte säkert kan lösas ska ge
INVESTIGATE i stället för ett automatiskt sanningsval.

Modellen får inte anta att filsystemets senast ändrad-tid innebär att filen är
gällande; tvetydighet eller en oläsbar/overifierbar kandidat på högsta normala
källnivå ger fortsatt INVESTIGATE. Exakt metadataformat, persistensgräns och
migrationsstrategi beslutas när den senare iterationen planeras.

#### Accepted design boundaries

- Deterministisk kod inventerar filer, normal artefaktstatus, DraftStore,
  projektfas och tillåtna operationer samt fastställer varje kandidat, action,
  blockerare och en av fyra hårda prioritetsnivåer.
- AI får endast rangordna kandidater inom samma nivå och komplettera med
  motivering, semantiska gap, osäkerhet och frågor.
- AI får inte skapa eller ta bort kandidater, ändra action eller nivå, passera
  blockerare, rangordna över en nivågräns eller exekvera något. Ett ogiltigt
  AI-resultat kastas helt och deterministisk fallback används.
- Varje råd anger åtgärdstyp, motivering, relevanta källor, osäkerheter och
  eventuella blockerande frågor.
- Högst tre prioriterade nästa steg visas.
- Rekommendationen är normalt transient och blir inte en ny permanent
  sanningskälla.
- Beroenden och readiness-regler återanvänder eller utökar befintligt
  artefaktregister och artefaktdefinitioner i stället för att skapa en separat,
  dold arbetsflödesmodell i promptar.
- Funktionen respekterar befintlig phase gating, `SourceSelector`, approval-
  livscykel och human-in-the-loop-princip.

#### Phased introduction

Sprint 6:s första version erbjuder den explicita funktionen `Rekommendera nästa
steg`, analyserar endast legitima projektkällor och returnerar högst tre
prioriterade råd. Normal projektartefakt och DraftStore är separata
state-dimensioner. Mjuk ordning är `Mål → Leveranser → WBS → Tidplan`,
Riskanalys är tvärgående och fallback inom en nivå är Mål, Riskanalys,
Leveranser, WBS, Tidplan.

Transient överhoppning får fylla på presentationen med nästa kandidat från
samma resultat/snapshot men ändrar inte kärnstate och startar ingen funktion.
Kontextprojektionen ska vara begränsad och testbar; 8 000 tecken per källa och
32 000 totalt är initiala verifierbara implementationsvärden, inte permanenta
produktregler.

Senare utveckling kan lägga till en kort processuppmaning efter approval eller
export, nya rekommendationer när en accepterad fil har placerats i
`Projektplanering` och kontexten laddats om, förändringsdetektion och stale-
markering, definierade beroenden mellan artefakter samt möjlighet för
projektledaren att uttryckligen starta en rekommenderad CREATE- eller REFINE-
operation.

## Architecture questions to revisit

- **Cross-artifact ID grounding:** determine how one artifact can verify
  stable internal IDs from another artifact through an explicit source
  boundary, without hidden reads from `AI-utkast`, sidecars, a global ID
  index, a database, or treating `Export` as project source.
- **Shared authoritative UU reference context:** investigate a future shared
  capability for grounded facts about Uppsala University's organization,
  UIT, e-förvaltning, ownership, responsibility, and service management. It
  must not become an artifact-specific shortcut or an unverified truth source.
- **UPDATE/scenario architecture:** design how `phase + operation` selects a
  scenario, sources, revision rules, traceability, and change summary for
  conservative execution-phase updates.
- **SharePoint/Microsoft 365 as a possible future platform direction:**
  explore SharePoint Online as the project portal and document surface, with
  project documents stored in SharePoint document libraries. The Python
  application would run in a suitable Azure service rather than directly in
  SharePoint, and would use Microsoft Graph for document access and Entra ID
  for identity and access control.

  Keep the model boundary provider-independent so Gemini could later be
  replaced by, for example, Azure OpenAI or a Microsoft 365 Copilot-based
  solution. Microsoft 365 Copilot is not a directly API-compatible replacement
  for Gemini. A declarative agent, a custom engine agent, and continued use of
  the existing backend are distinct alternatives that need evaluation.

  Reuse the existing domain models, codecs, approval flows, and Office
  rendering where practical. Before any platform decision, investigate
  security, information classification, the permission model, `Sites.Selected`,
  logging, operations, licensing, and long-term governance and maintenance.
  The next decision point is a demonstration of the current solution followed
  by an architecture discussion with UIT's architects and other affected
  stakeholders. No target architecture or implementation has yet been decided
  or prioritized.
- **Finer source grounding:** add page-, section-, or identifier-level
  grounding only when a concrete artifact contract requires it.

## Technical debt to monitor

- `requirements.txt` beskriver inte hela runtime-miljön. I en ren Python
  3.14-venv kunde Streamlit installeras från filen, men applikationen kunde
  inte starta innan bland annat `google-genai`, `openpyxl`, `python-docx`,
  `pypdf`, `python-pptx` och `PyYAML` installerades manuellt. Ett separat
  framtida förbättringsarbete behöver göra runtime-beroendena kompletta och
  verifiera installation och start i en ren miljö; detta ingår inte i Sprint
  7 Task 7.
- Validation, canonicalization, mutation, persistence, and rollback/failure
  isolation repeat in parts of the lifecycle. Monitor the seam, but introduce
  no abstraction until several concrete cases justify one.
- Modernize legacy and prototype tests that use obsolete imports, retired
  APIs, or hardcoded local files; do not restore obsolete production paths to
  make them pass.
- Consolidate duplicated Gemini client usage behind the existing service
  direction when it simplifies supported runtime code.
- Remove superseded workflow or procedural paths only after their replacements
  are connected and verified.
