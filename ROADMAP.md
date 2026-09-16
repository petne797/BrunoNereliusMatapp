# Roadmap

This roadmap stays intentionally small. `SPRINTS.md` contains the active work.


## Vision

Projektassistenten ska bli en AI-driven projektpartner som hjälper projektledare genom hela projektets livscykel.

Arkitekturen ska vara generell och återanvändbar. Nya funktioner ska i första hand införas genom nya artefaktdefinitioner, nya operationer och nya workflows – inte genom duplicerad affärslogik.

---

# Milestone 1 – Planning Lifecycle Foundation ✅

## Status

Completed

## Syfte

Bygga den första kompletta vertikala implementationen av artefaktlivscykeln.

Riskanalys används som referensartefakt för att etablera den generella arkitekturen.

Den implementerade livscykeln omfattar:

```text
Create
    ↓
Workflow
    ↓
ArtifactContext
    ↓
Source Selection
    ↓
RiskAnalysisGenerator
    ↓
ArtifactDraft
    ↓
Revision
    ↓
Approval
    ↓
GeneratedDocument
    ↓
Versioned DOCX
```

Denna livscykel är nu implementerad och verifierad med:

- fokuserade enhetstester
- integrationstest
- manuella tester mot riktig Gemini

Resultatet är den arkitektur som framtida artefakter ska återanvända.

---

# Milestone 2 – Stateful Project Assistant ✅

## Status

Completed

## Syfte

Göra agenten robust över flera interaktioner och omstarter.

Fokusområden:

- State-aware tool selection
- Canonical project information structure
- Persistent ArtifactDraft
- Restore project drafts after restart

När milstolpen är klar ska projektledaren kunna:

```text
Skapa utkast

↓

Revidera

↓

Stäng programmet

↓

Starta igen

↓

Fortsätta exakt där arbetet avslutades
```

Agenten ska upplevas arbeta med ett pågående arbetsflöde snarare än separata frågor.

Flera artefaktutkast kan finnas samtidigt och identifieras av artefakttyp.

---

# Milestone 3 – Planning Artefacts

## Syfte

Sprint 3 börjar med att validera den färdiga Sprint 2-plattformens
återanvändbarhet genom att implementera fler planeringsartefakter.

Status: Sprint 3–6 är slutförda. Riskanalys, Tidplan, Mål, Leveranser och WBS
använder den gemensamma artefaktlivscykeln och är verifierade genom sina
stödda CLI-, Streamlit- och Office-flöden. Sprint 6 kompletterade produkten med
Planeringsnavigatorn, demo-QA och SharePoint/M365-discovery.

Återanvänd den etablerade livscykeln för fler planeringsartefakter.

Nu stödda artefakter:

- Riskanalys
- Tidplan
- Mål
- Leveranser
- WBS
- Intressentanalys
- Kommunikationsplan

Återstående planerade artefakter omfattar:

- Kommunikationsplan
- Projektplan

### Beslutad riktning efter Sprint 6

Den beslutade leveransordningen är:

1. **Sprint 6 – Planeringsnavigator och demo readiness, avslutad.** Den
   nuvarande planeringsprodukten och den rådgivande Navigatorn är verifierade
   med befintligt Streamlit-UI; SharePoint/M365-discoveryn är genomförd.
2. **Sprint 7 – Intressentanalys och Kommunikationsplan, avslutad och
   produktmässigt accepterad.** De två relaterade men separata
   strukturerade artefakterna har kompletta vertikala slices. Det gemensamma
   breda källkontraktet, Tidplanens stabila Huvudaktiviteter (`TPG-...`) och
   Navigatorstöd för samtliga sju planeringsartefakter är implementerade.
   Slutgrinden Task 7 är tekniskt verifierad, Chat-reviewad och accepterad
   efter manuell Product Owner-QA av Planeringsnavigatorn i Streamlit.
3. **Sprint 8 – Projektplan.** Genomför enligt
   [discoverykontraktet](plans/sprint8-projectplan-discovery.md) och
   [Sprint 8-planen](plans/sprint8-projectplan.md). Projektplan är
   planeringsstadiets sammanhållande slutartefakt; implementationen följer
   efter Task 0 och den avgränsade runtime-enablementen i Task 0A.
4. **Beslutspunkt efter Sprint 8.** Genomför ett avgränsat användartest med ett
   mindre antal projektledarkollegor. Resultatet ska styra vilken produktfas
   som prioriteras därefter: större UI-/API-arbete med Svelte,
   genomförandefasens Project Execution/UPDATE eller en fokuserad
   förbättringssprint.

Det nuvarande Streamlit-gränssnittet behålls till och med användartestet om det
är tillräckligt användbart för ett sammanhängande planeringsflöde. En större
UI-remake prioriteras därför inte före att planeringsstadiet är komplett.
Användartestet är en produktvalidering i liten skala, inte en teknisk
fleranvändarutrullning eller ett beslut om delad lagring och drift.

Sprint 7 har separat produkt-discovery och detaljerad taskplan i
`plans/sprint7-artifact-discovery.md` respektive
`plans/sprint7-stakeholder-communication.md`. Sprint 8 har motsvarande
detaljerat kontrakt och plan i `plans/sprint8-projectplan-discovery.md` och
`plans/sprint8-projectplan.md`. Beslutspunkten efter Sprint 8 ska bevara
handlingsutrymmet i stället för att i förväg låsa nästa produktfas.

För varje artefakt ska samma livscykel användas:

```text
Create
    ↓
Refine
    ↓
Approve
    ↓
Export
```

Skillnaden mellan artefakterna ska i huvudsak ligga i:

- artefaktdefinition
- presentationsoberoende domänobjekt och strikt codec där strukturen kräver det
- source selection, generatorregler och validering
- artefaktspecifik Office-presentation

Inte i duplicerad lifecycle-, state-, persistens-, approval- eller
exportorkestrering.

## Prioriterad leveransordning

Planeringsartefakterna har en logisk best-practice-relation:

```text
Mål → Leveranser → WBS → Tidplan
```

Relationen är inte en obligatorisk arbetsordning. Planeringsarbetet är
iterativt och händelsestyrt, så artefakten med tillräckligt underlag kan
skapas eller förfinas först och senare bidra till de andra. AI-utkast blir
aldrig vanliga projektkällor i denna iteration.

Den beslutade prioriterade leveransordningen är:

1. **Sprint 4 – Mål och Leveranser (genomförd).** De två artefakterna
   återanvänder den etablerade livscykeln med strikt Markdown, spårbara
   relationer och Word-export.
2. **Sprint 5 – WBS i Excel samt WBS–Tidplan-samspel (genomförd).** WBS
   använder en Excel-presentation utan att Excels layout blir domänmodell, och
   relationen till Tidplan är implementerad och tydligt avgränsad.
3. **Sprint 6 – Planeringsnavigator och demo readiness (genomförd).** Den
   deterministiskt avgränsade rådgivande Navigatorns första femartefaktsslice
   infördes utan en linjär wizard.
4. **Sprint 7 – Intressentanalys och Kommunikationsplan (genomförd).**
   Båda strukturerade XLSX-slicerna och Navigatorns integration med sju artefakter
   är tekniskt genomförda. Intressentanalys och Tidplan används som starka
   mjuka föregångare till Kommunikationsplan, aldrig hårda krav. Sprintens
   slutgrind är Chat-reviewad och produktmässigt accepterad efter manuell
   Navigator-QA i Streamlit.

Sprint 5 är slutförd. WBS använder det beslutade produkt-, domän- och
presentationskontraktet med resultatperspektivet som default och beskriver
projektets hierarkiska arbetsstruktur genom stabila `WBSNode`-ID:n, explicit
hierarki, `Arbetsområde` och `Arbetspaket`. Domänen har inget fast maximalt
djup och arbetspaket är löv. WBS-noder kan relateras till `Deliverable.id`, så
Goal-spårbarhet kan härledas via Deliverable.

WBS och Tidplan förblir separata domäner. Den valfria relationen från
`ScheduledActivity` till ett WBS-arbetspaket och WBS-aware CREATE/REFINE är
implementerade. Tidplan prioriterar en unik schemaidentifierad normal WBS-XLSX,
validerar transient att non-null-referenser finns och avser `Arbetspaket` och
fungerar fortsatt utan WBS med `null`-relationer. WBS är inte en obligatorisk
predecessor för Tidplan. Excelpresentationen och den WBS-specifika renderern
implementerades i Task 5. WBS → Deliverable verifierar nu varje non-empty
relation mot exakta ID:n i en unik, strukturellt verifierad normal
Leveranser-DOCX under aktiv projektrot. Den etablerade source boundaryn kring
AI-utkast, sidecars, globalt ID-index, databas och Export som projektkälla
kvarstår.

### Sprint 4 – avslutad discovery och produktkontrakt

Sprint 4 inleddes med Task 0, som inspekterade PPS-underlag, Mål-mall och
aktuell kod innan Goal-kontraktet beslutades: relationer mellan Effektmål och
Projektmål, obligatoriska och valfria fält, öppna frågor och
ändringsförslag, source-selection- och promptregler, validering, kanoniskt
persistent format samt DOCX-presentation.

Produkt- och källregler fastställs före implementation och rendererdesign.
Mallen får inte styra domänmodellen.

Följande behov är fortsatt planerade men prioriteras efter fler
planeringsartefakter:

- skapa och redigera projekt med korrekt mappstruktur
- visa och redigera projektmetadata, såsom namn, fas, status och projektledare
- förbättra den lokala demons UX, layout, navigering och återkoppling
- exportera samma godkända Riskanalys till både DOCX och XLSX

Varje ny Office-artefakt ska föregås av en template-readiness-kontroll. Innan
en mall betraktas som produktionsklar ska QA normalt omfatta automatiska
tester, normal öppning i lokalt installerat Microsoft Word eller Excel,
representativ output och mänsklig visuell/product review. Legacy- och
prototyptester ska moderniseras separat så att den ordinarie testsamlingen blir
fullt körbar.

## Later cross-cutting discovery

Flera bredare behov ska bevaras för senare analys utan att automatiskt bli
scope för den aktiva sprinten:

- en delad, auktoritativ referenskontext om Uppsala universitets organisation,
  Universitetsförvaltningen, UIT, e-förvaltning, ansvar och tjänsteförvaltning;
- en tydligare UPDATE-/scenarioarkitektur för Project Execution, där
  `phase + operation` styr scenario, source selection, `ArtifactContext`,
  konservativ revision samt spårbar ändringssammanfattning. Sprint 7-
  discoveryn har bevarat en preliminär informationsmodell för senare arbete:
  `Styrdokument/**` är aktuell fastställd baseline; `Styrgrupp/**` är
  kronologisk händelsehistorik där relevanta möten väljs explicit eller genom
  ett deterministiskt föreslaget urval som projektledaren bekräftar; inom ett
  valt möte väger preliminärt `Beslut > Minnesanteckningar > Statusrapport ≈
  Presentation`. Senare beslut ska fungera som förändringsauktoritet i en
  explicit UPDATE, inte skapa en dold alternativ plan. Exakt lifecycle,
  provenance, statusrapportering och source selection beslutas först när
  genomförandefasen planeras;
- en möjlig framtida SharePoint/Microsoft 365-riktning för projektportal,
  dokumentyta och Azure-hostad applikation. Sprint 6 Task 6 genomförde den
  första discoverygrinden; målarkitektur och prioritering är fortsatt inte
  beslutade och nästa beslutspunkter finns i discoveryunderlaget och
  `IDEAS.md`;
- en rådgivande Planeringsnavigator som prioriterar möjliga nästa steg från
  legitima projektkällor utan att själv utföra dem. Den första slicen är
  implementerad och accepterad genom Sprint 6 enligt det godkända produkt- och
  arkitekturkontraktet. Sprint 7 har utökat och produktmässigt accepterat
  samma modell med Intressentanalys och Kommunikationsplan;
- en senare freshness- och proveniensutbyggnad av Planeringsnavigatorn. Den
  ska kombinera tidsstämplar för skapande/revision, mänskligt godkännande och
  export med stabil innehållsversion eller innehållsfingeravtryck samt
  spårbara `baserades på`-relationer mellan käll- och artefaktversioner.
  Exporttid får inte ensam avgöra aktualitet eller vilken motstridig uppgift
  som är sann. Exakt datamodell och leveransiteration planeras separat.

Delad auktoritativ UU-referenskontext, UPDATE-/scenarioarkitektur och den
slutliga M365-riktningen är fortsatt discoveryfrågor utan implementerat
arkitekturbeslut. Planeringsnavigatorns rådgivande hybridkontrakt är
implementerat med deterministisk eligibility, action, blockerare och hårda
prioritetsnivåer samt endast inomnivå-rangordning av AI. Kärnan,
applikationsgränsen, Streamlit-integrationen samt Sprint 6:s samlade produkt-
och demo-QA är genomförda och accepterade.

## Sprint 6 accepted iteration slice

Planning supports a second iteration through baseline REFINE from a UNIQUE
accepted normal artifact and clean-slate CREATE with current legitimate
sources and AI-Input. Both reuse the established lifecycle and require human
acceptance before an export becomes a normal project artifact. Freshness and
provenance remain future work. Sprint 7:s ADR-018 breddar vilka läsbara
planeringsdokument som är legitima källor utan att ändra denna lifecycle-
semantik.

## Local demo interface

Ett tunt lokalt Streamlit-gränssnitt är implementerat för demonstration och
enklare användning av Riskanalys, Mål, Leveranser, WBS, Tidplan och
Intressentanalys samt Kommunikationsplan.

Webbgränssnittet ska återanvända samma applikationsgräns, workflows,
`DraftStore`, godkännande, persistens och export som CLI:n. CLI:n ska finnas
kvar. Streamlit är endast ett presentationslager och får inte bli en separat
väg för affärslogik, tillstånd eller persistens. Den lokala demon omfattar nu
samtliga sju implementerade planeringsartefakter med deterministiska,
state-korrekta funktioner, funktionsspecifikt medskick och separat fri dialog.
Streamlit behålls genom den planerade kompletteringen av planeringsstadiet och
det efterföljande avgränsade användartestet, förutsatt att gränssnittet är
tillräckligt användbart. En generell UI-remake och bredare UX-, layout- och
navigeringsförbättringar är fortsatt senare arbete.

När UI/UX-arbetet senare återupptas är Svelte det valda frontendramverket.
SvelteKit kan utvärderas som applikationsram när den sprinten planeras.
Ett sådant gränssnitt ska använda en tydlig applikations-/API-gräns mot
Python-systemet och inte duplicera domänlogik, tillstånd, persistens,
approval eller Office-rendering; CLI:n kan finnas kvar parallellt. Svelte,
ett nytt API, autentisering, driftsättning och frontendstruktur ingår inte i
Sprint 7 och kan kräva ett senare ADR.

Den senare UI-/webbramverkssprinten ska också pröva progressiv visning av
strukturerade artefakter. I stället för att primärt visa hela canonical-
strukturen som rå text bör gränssnittet först visa exempelvis namn eller rubrik
och en kort beskrivning per post, med möjlighet att fälla ut analys-, relations-,
proveniens- och metadatafält. Detta ska vara ett rent presentationslager ovanpå
samma domän och lifecycle, inte en alternativ persistent representation eller
Office-sanning.

När denna ännu inte numrerade UI-/webbramverkssprint planeras ska även det
deterministiska kreativitetsvalet från `IDEAS.md` tas upp för beslut. Det kan
ge projektledaren nivåerna Källnära, Balanserad och Utforskande för CREATE och
REFINE, medan funktionsspecifik fritext finns kvar. Funktionen är en kandidat
för samma interaktionsdesignarbete och är inte prioriterad som en separat
implementation i nuvarande Streamlit-UI.

Efter UI-/webbramverksgenomgången ska en separat pilot utvärdera en gemensam
projektrot som synkroniseras lokalt via OneDrive. Koden ska fortsatt delas via
Git/GitHub och ligga utanför den synkroniserade projektytan. Varje lokal
installation ska använda en konfigurerad, användarspecifik sökväg till samma
delade yta i stället för en hårdkodad maskinsökväg; Streamlit har redan ett
första sådant stöd genom `PROJECT_ASSISTANT_PROJECTS_ROOT`.

Piloten ska föregå utrullning till kollegor och skarp dokumentmigrering. Den
ska börja med ett kopierat syntetiskt projekt och verifiera lokal
filtillgänglighet, projektisolering, runtime-/source-gränsen, synkfel,
konfliktkopior, sökvägslängd samt samtidiga skrivningar till `AI-utkast` och
versionsnumrerad `Export`. Initialt kan **en aktiv skrivande projektledare per
projekt** vara en uttrycklig driftregel; OneDrive-synk ger inte
transaktioner eller distribuerad låsning. Exakt delning av utkast och export,
liksom senare konfliktvarning eller låsning, beslutas före implementation.

Aktiviteten är en möjlig mellanlösning och ersätter inte den separata
SharePoint/M365-discoveryn om portal, Graph, Entra, Azure-hosting, säkerhet och
långsiktig fleranvändararkitektur. Den detaljerade pilotidén och dess
avgränsningar finns i `IDEAS.md`.

### Deterministic action routing for local Web UI

Sprint 6 Task 1 har implementerat en första avgränsad deterministisk
funktionsrouting i det lokala webbgränssnittet. Användaren väljer artefakt och
får endast de funktioner som är giltiga för implementerad capability,
projektfas och
utkaststatus. `Inget arbetsutkast` ger `Skapa [artefaktnamn]`, `AI_DRAFT` ger
Visa, Revidera och Godkänn, och `approved` ger Visa, Revidera och Exportera.
CLI och webb ska fortsatt använda samma underliggande publika actions.
Frivillig kompletterande fritext är indata till den redan valda funktionen,
inte routingmekanism, medan fri dialog med projektassistenten har en separat
och tydligt märkt väg.

Förmågan återanvänder befintliga lifecycle-, behörighets- och
projektisoleringskontroller. Streamlit äger inte state och skapar ingen
parallell lifecycle. Streamlit förblir en begränsad lokal demo;
Svelte/SvelteKit, produktions-API, autentisering och driftsättning ligger
fortsatt senare.

---

# Milestone 4 – Project Execution

## Syfte

Stödja projektets genomförandefas.

Fokus flyttas från att skapa dokument till att hålla dem aktuella.

Planerade funktioner:

- UPDATE av styrande dokument
- Statusrapport till styrgrupp
- Statusrapport till portföljledning
- Identifiera avvikelser mot plan
- Beslutsunderlag
- Tväranalys mellan flera dokument
- Konfliktdetektering
- Rekommenderade åtgärder

Agenten ska här arbeta betydligt mer konservativt än under planeringen.

Sprint 7-discoveryn bevarar endast ovanstående preliminära informationsriktning
för senare diskussion. Milestone 4:s faktiska source-, UPDATE-,
statusrapporterings- och webbinteraktionskontrakt ska fastställas när arbetet
med genomförandefasen påbörjas.

---

# Milestone 5 – Project Closure

## Syfte

Stödja projektets avslut.

Planerade funktioner:

- Slutrapport
- Erfarenhetsåterföring
- Överlämningsunderlag
- Projektsammanfattning
- Projektutvärdering

---

# Milestone 6 – Intelligent Project Assistant

## Syfte

Utveckla Projektassistenten från dokumentgenerator till aktiv projektpartner.

Exempel på framtida funktioner:

- Proaktiv identifiering av risker
- Förslag på nästa aktiviteter
- Påminnelser om saknade beslut
- Identifiera motsägelser mellan projektartefakter
- Identifiera informationsluckor
- Kvalitetsgranska projektets dokumentation
- Föreslå förbättringar
- Stöd vid styrgruppsförberedelser
- Tvärprojektanalys
- Återanvändning av erfarenheter från tidigare projekt

Denna milstolpe bygger ovanpå den etablerade livscykeln och den stateful arkitekturen.

---

# Arkitekturprincip

Projektassistenten utvecklas genom små vertikala förbättringar.

Varje ny funktion ska i första hand återanvända den etablerade arkitekturen:

```text
Artefakt
        +
Operation
        +
Projektfas
        ↓
Workflow
        ↓
ArtifactContext
        ↓
Generator
        ↓
ArtifactDraft
        ↓
Approval
        ↓
GeneratedDocument
        ↓
Export
```

Målet är att minimera ny affärslogik och istället utöka systemet genom konfiguration, artefaktdefinitioner och återanvändbara komponenter.
