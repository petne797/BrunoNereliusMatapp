# Sprints – statusindex

**Aktuellt läge: Sprint 8 Task 3 – Word- och artefaktkvalitet – pågår på
task-branch `s08-t03-projectplan-word-quality`. Task 2 – Komplett vertikal
slice och första DOCX – är genomförd och squash-mergad i `eb3bedb`. Task 1 – Projektplanens kärna, källor och
iteration – är genomförd och squash-mergad i `fb24caf`. Sprint 7 är avslutad
och produktmässigt accepterad.**

**Senaste tidigare accepterade grind: Sprint 7 Task 6 – Kommunikationsplan
vertikal slice och XLSX, mergad i `3f45426` efter Chat-review samt riktig
Gemini- och native Excel-QA. Sprint 7:s slutliga accepterade grind är Task 7;
PR #11 squash-mergades i `ca231bfa`.**

## Sprint 8 – taskstatus

Detaljerat scope, beroenden, QA och Definition of Done finns i
[Sprint 8-planen](plans/sprint8-projectplan.md). Det fastställda produkt- och
arkitekturkontraktet finns i
[discoveryunderlaget](plans/sprint8-projectplan-discovery.md).

| Task | Innehåll | Status |
|---|---|---|
| 0 | Discovery, kontrakt och sprintplan | Genomförd och mergad i `58a2e894` |
| 0A | Reproducerbar runtime | Genomförd och mergad i `f2ad9da` |
| 1 | Projektplanens kärna, källor och iteration | Genomförd och squash-mergad i `fb24caf` |
| 2 | Komplett vertikal slice och första DOCX | Genomförd och squash-mergad i `eb3bedb` |
| 3 | Word- och artefaktkvalitet | Pågår på task-branch; native Word-QA återstår |
| 4 | Navigator, Streamlit-samspel och Sprint 8-QA | Planerad |

## Sprint 7 – taskstatus

Detaljerat scope, acceptanskriterier och Definition of Done finns i
[Sprint 7-planen](plans/sprint7-stakeholder-communication.md). Produkt- och
källkontrakten finns i [discoveryunderlaget](plans/sprint7-artifact-discovery.md).

| Task | Innehåll | Status |
|---|---|---|
| [0](plans/sprint7-stakeholder-communication.md#task-0--discovery--och-planeringsgrind) | Discovery- och planeringsgrind | Genomförd, produktmässigt accepterad och mergad i `6014ac2` |
| [1](plans/sprint7-stakeholder-communication.md#task-1--gemensamt-planeringskällkontrakt) | Gemensamt planeringskällkontrakt | Genomförd, produktmässigt accepterad och mergad i `8511b92` |
| [2](plans/sprint7-stakeholder-communication.md#task-2--tidplan-huvudaktiviteter) | Tidplan Huvudaktiviteter | Klar; produktmässigt accepterad efter native Excel-QA |
| [3](plans/sprint7-stakeholder-communication.md#task-3--intressentanalys-domän-codec-och-analysregler) | Intressentanalys domän, codec och analysregler | Genomförd, produktmässigt accepterad och mergad i `160c43a` |
| [4](plans/sprint7-stakeholder-communication.md#task-4--intressentanalys-vertikal-slice-och-xlsx) | Intressentanalys vertikal slice och XLSX | Genomförd, reviewgodkänd och produktmässigt accepterad; mergad i `ab5a5f6` |
| [5](plans/sprint7-stakeholder-communication.md#task-5--kommunikationsplan-domän-och-verifierade-relationer) | Kommunikationsplan domän och verifierade relationer | Genomförd och mergad i `116ab6f` |
| [6](plans/sprint7-stakeholder-communication.md#task-6--kommunikationsplan-vertikal-slice-och-xlsx) | Kommunikationsplan vertikal slice och XLSX | Genomförd, Chat-reviewad och produktmässigt accepterad efter riktig Gemini- och native Excel-QA |
| [7](plans/sprint7-stakeholder-communication.md#task-7--planeringsnavigator-samspel-och-sprint-7-qa) | Planeringsnavigator, samspel och Sprint 7-QA | Tekniskt verifierad, Chat-reviewad och produktmässigt accepterad efter genomförd Product Owner-QA |

Task 4 återanvänder den gemensamma planeringslivscykeln för Intressentanalys:
bred source selection, strict canonical Markdown/YAML, CREATE och baseline-
REFINE, atomisk persistens och restore, approval utan provenance-omskrivning
samt transient typed export till en allowlistad XLSX-renderer. Arbetsboken har
huvudtabell, fyra Intresse×Inflytande-kvadranter med en separat grupp för ännu
ej placerade intressenter och ett blad för öppna frågor. Interna enum- och
proveniensvärden översätts till svenska endast i Office-presentationen.
Efter review- och formatteringskorrigeringarna gav den nätfria verifieringen
**1 109 passerade och 10 överhoppade tester**; representativ output finns i
`qa/Intressentanalys-QA.xlsx`. Riktig Gemini-QA, native Excel-QA och
produkt-QA är genomförda och fynden är korrigerade.

Task 7 utökar samma rådgivande Navigatorgräns till exakt sju artefakter och
bevarar applikationsägd eligibility, action, blockerare och prioritetsnivå.
Intressentanalys och Tidplan är mjuka föregångare till Kommunikationsplan;
CREATE blockeras inte när någon av dem saknas. Normal Kommunikationsplans
verifierbara STK-/TPG-/aktivitetsrelationer och Huvudaktivitetsordning
kontrolleras på nytt efter kontextomladdning och ogroundade relationer ger
`INVESTIGATE` utan mutation. Slutlig fokuserad verifiering gav 551 passerade
och 8 överhoppade tester. Supported regression gav 1 169 passerade och 10
överhoppade tester med de två kända tredjepartsvarningarna. Riktig Gemini-QA
mot syntetiskt icke-känsligt projekt verifierade båda kompletta
CREATE/REFINE→review→approval→XLSX-flödena, 6 STK-relationer, 3 ordnade
Huvudaktiviteter och 1 milstolpe. Tidigare native Excel/content-QA från Task
2, 4 och 6 återanvändes; Task 7:s nya syntetiska XLSX kontrollerades tekniskt.
Den manuella Product Owner-QA:n 2026-09-15 startade Streamlit-demot, körde
Planeringsnavigatorn och verifierade att Intressentanalys och
Kommunikationsplan visades korrekt bland rekommendationerna. Beteendet
accepterades. Chat-review av PR #11 fann inga blockerande kod- eller
arkitekturfynd.

## Sprint 6 – taskstatus

Task 4B implementerades från `master`/`origin/master` på `36bdd2a`,
verifierades och godkändes i separat review enligt den tidigare
samarbetsmodellen och committades i `0b46d26`. Skyddade användarändringar
bevarades.

| Task | Innehåll | Status |
|---|---|---|
| [0](plans/sprint6-planning-navigator.md#task-0) | Demo baseline och Sprint 6-kontrakt | Genomförd |
| [1](plans/sprint6-planning-navigator.md#task-1) | Deterministiskt webb-UI och demo-stabilisering | Genomförd; separat reviewgodkänd |
| [2](plans/sprint6-planning-navigator.md#task-2) | Navigatorns produkt- och arkitekturkontrakt | Genomförd; kontrakt godkänt |
| [3](plans/sprint6-planning-navigator.md#task-3) | Navigatorns domän och analysmotor | Genomförd; verifierad, separat reviewgodkänd och committad i `dd7a455` |
| [3a](plans/sprint6-planning-navigator.md#task-3a) | Bootstrapindex och bevarad sprinthistorik | Genomförd och verifierad; dokumenterad i lokal commit `605c00f` |
| [4](plans/sprint6-planning-navigator.md#task-4) | Navigatorns integration och UI | Genomförd och verifierad; separat reviewgodkänd |
| [3B](plans/sprint6-followup-tasks.md#task-3b) | Stabil testmiljö för Codex | Implementerad, verifierad och separat reviewgodkänd |
| [4A](plans/sprint6-followup-tasks.md#task-4a) | Grounded creativity vid CREATE och REFINE | Genomförd, verifierad, separat reviewgodkänd och committad i `9280a13` |
| [4B](plans/sprint6-followup-tasks.md#task-4b) | Ny REFINE-iteration från accepterad baseline samt clean-slate CREATE | Genomförd, verifierad, separat reviewgodkänd och committad i `0b46d26` |
| [5](plans/sprint6-planning-navigator.md#task-5) | Navigator-, lifecycle- och demo-QA | Genomförd, tekniskt verifierad, Chat-reviewad och produktmässigt accepterad |
| [6](plans/sprint6-planning-navigator.md#task-6) | SharePoint/M365-discovery | Genomförd, Chat-reviewad och produktmässigt accepterad; [underlag](plans/sprint6-m365-architecture-discovery.md) |

Navigatorns kärna, applikationsintegration och explicita Streamlit-UI är
implementerade. Task 4 tillför en publik read-only-anropgräns, en konkret
Gemini-ranker med strikt strukturerat svar och deterministisk fallback samt
projektbunden presentationsstate med skip och livscykel-invalidering. UI:t visar
högst tre rådgivande rekommendationer och utför aldrig åtgärden åt användaren.

Task 4 har verifierats med 95 fokuserade Navigator-/Streamlit-tester, 72 tester
för Task 3- och dokumentflöden, 59 närliggande regressionsfall samt den breda
stödda sviten: 695 godkända och 1 överhoppat test. Gemini-anropen var mockade;
ingen extern AI-tjänst anropades. Ändrade Pythonfiler kompilerades och den lokala
Streamlit-serverns hälsokontroll svarade med HTTP 200. En separat kodreview
godkände Task 4 efter en egen fokuserad omkörning med 95 passerade tester.
Task 5:s samlade tekniska, manuella och verksamhetsmässiga QA är nu genomförd
och accepterad. Sprint 6 är avslutad efter genomförd och accepterad Task 6.

Task 3B inför `scripts/run_tests.py` som enda korta entrypoint för den explicit
manifesterade, nätfria stödda regressionen med 93 supported-moduler och för
fokuserade tester. Runnern förankrar alltid pytest i reporoten, begränsar
fokuserade paths till `tests/`, använder en unik systemtemp per körning och
stänger ute 15 dokumenterade legacy-/prototypmoduler. Standardkommandona är:

```powershell
.\.venv\Scripts\python.exe scripts\run_tests.py supported -q
.\.venv\Scripts\python.exe scripts\run_tests.py focused tests\test_<område>.py -q
```

Discovery bekräftade att direkt pytest från en annan startkatalog inte hittar
`agent`, medan Codex sandbox på Windows ger `WinError 5` när pytest sätter ACL
på både sin återanvända standardtemp och en unik `--basetemp`. Det senare är en
exekveringsmiljöbegränsning och har inte byggts runt med ändrade ACL:er eller
fixtures; Codex kör standardkommandot med normal/utökad processbehörighet.
Office-/COM-verifiering förblir separat native QA. Efter reviewkorrigeringarna
gav verifieringen 32 passerade runner-tester och 1 Windows-symlinkskip från
`plans/` samt en bred körning med 727 passerade, 2 Windows-skip och endast de
två kända varningarna. Ingen riktig Gemini- eller nättrafik användes och
Git-status under `projects/` var oförändrad. Task 3B är separat reviewgodkänd.

Task 4A harmoniserar CREATE- och REFINE-prompterna för Riskanalys, Mål,
Leveranser, WBS och Tidplan. CREATE får aktivt skapa professionella
planeringsförslag men får inte framställa styrande fakta, beslut, ansvar,
organisation, datum eller andra faktaluckor som källbelagda projektfakta.
Artefakternas befintliga source references, öppna frågor,
förändringsförslag, beskrivningar och Riskanalys-sektioner skiljer
källgrundning från AI-härledda förslag och materiella antaganden. REFINE får
komplettera och omstrukturera men ska bevara välgrundat och fungerande innehåll
om varken legitimt underlag eller funktionsspecifik instruktion motiverar en
ändring. Funktionsspecifik fritext är uttryckligen instruktion, inte
källbevis. Draft-approval gäller fortsatt bara export och gör inte innehållet
till en normal projektkälla; UPDATE-beteendet är oförändrat.

Task 4A:s funktionsspecifika fritext styr nu också kreativiteten explicit utan
ny UI-kontroll, keyword-routing eller persistent state. Utan uttrycklig
kreativitetsinstruktion används ett balanserat standardläge. En uttrycklig
källnära/source-only-instruktion begränsar fria AI-hypoteser till direkt
källstött innehåll och befintliga luckmekanismer, medan en utforskande
instruktion tillåter fler tydligt preliminära planeringsförslag.
Kreativitetsinstruktionen får endast styra förslagens mängd och typ och kan
aldrig åsidosätta source authority, källreferens-, Effektmål-, codec-,
grounding-, human-in-the-loop- eller approvalregler.

Verifieringen gav 88 passerade prompt-/generator-/lifecycle-tester. En bredare
fokuserad körning av workflows, codecs, source selection och grounding,
DraftStore-atomicitet, approval och restore gav 268 passerade och 1
Windows-symlinkskip. Den fulla stödda regressionen gav 733 passerade och 2
Windows-skip med endast de två kända tredjepartsvarningarna. Ändrade Python-
och testfiler kompilerades, `git diff --check` passerade och ingen riktig
Gemini- eller nättrafik användes. Task 4A är implementerad, verifierad och
separat reviewgodkänd.

Task 4B inför **Revidera accepterad [artefaktnamn]** och **Skapa ny
[artefaktnamn] från grunden** för exakt Riskanalys, Mål, Leveranser, WBS och
Tidplan i Planering. REFINE använder en unikt verifierad normal baseline;
clean-slate CREATE utesluter gamla normala filer av samma typ före läsning.
Båda ignorerar tidigare draftinnehåll och laddar aktuella legitima källor,
inklusive styrning, formella beslut och AI-Input. Befintliga workflows,
source selection, generatorer och approval/export återanvänds. De accepterade
normalfilerna ändras aldrig automatiskt.

Ersättningsvarningen visas före det explicita klicket, även för ett godkänt
utkast som kan vara oexporterat. Generering och validering föregår ersättning
med ett nytt AI_DRAFT. Atomisk Markdown-ersättning och DraftStore-rollback
bevarar tidigare innehåll vid fel. Projekt- och store-bindningar kontrolleras
före och efter genereringen. Word-tabeller och samtliga Excel-blad läses nu
för att baselineinnehåll inte ska tappas.

Efter korrigering av fem reviewfynd gav Task 4B **398 passerade och 9
överhoppade fokuserade tester**, ytterligare **2 passerade katalogtester**
och **984 passerade samt 10 överhoppade tester** i den fulla stödda
regressionen på 185,79 s, med två kända tredjepartsvarningar. Regressionerna
täcker runtimealias, inventeringsfel, commitpunkt, hela clean-slate-UI:t
och aktivt Excel-blad inom generatorns faktiska kontextgräns. Åtta nya
symlinkfall kräver Windows-rättighet som saknas; junctionfallen passerade.
Manifestet omfattar 94 moduler. Kompileringskontroll av 31 Pythonfiler,
`git diff --check` och lokal Streamlit-hälsokontroll (HTTP 200) passerade.
Gemini var mockad och ingen extern nättrafik användes. Git-status under
`projects/` var identisk före och efter. Detta var korrigeringskörningens
mellanstatus; mänsklig interaktiv UI- och native Office-granskning återstår
till Task 5. Faktiska resultat och verifieringsbegränsningar finns i Task
4B-avsnittet i uppföljningsplanen.

Det sista blockerande P1-fyndet korrigerades 2026-09-09 i den gemensamma
Markdown-persistensgränsen. `AI-utkast` måste nu vara den fysiska katalogen
direkt under exakt aktiv projektrot, utan junction, symlink eller annan
reparse-omdirigering. Målkatalog och målfil verifieras på nytt vid persistens;
Task 4B gör dessutom en tidig kontroll före AI och en kontroll efter AI.
Elva riktiga Windows-junctionfall och två normala kontrollfall gav **13
passerade**. Samma testurval gav före korrigeringen **11 fel och 2 passerade**.
Den fokuserade regressionen gav **387 passerade och 8 överhoppade**; hela
`scripts/run_tests.py supported -q` gav **997 passerade och 10 överhoppade**.
Kompilering, `git diff --check` och Streamlit-hälsokontroll (**HTTP 200 ok**)
passerade. De åtta Task 4B-symlinkfallen är fortsatt plattformsskip, medan de
nya junctionfallen körs och passerar utan skip. Ingen riktig Gemini- eller
extern nättrafik användes. Skyddad Git-status var identisk före och efter.
Detta var P1-korrigeringens mellanstatus före den slutliga omreviewn.

Slutlig fokuserad omreview genomförd 2026-09-10: inga blockerande eller nya
kodfynd. Tretton riktiga Windows-junctionfall passerade utan skip. Den
fokuserade Task 4B-sviten gav 387 passerade tester och 8 symlinkskip; den
stödda regressionen gav 997 passerade och 10 överhoppade tester. Symlinkskippen
är en kvarvarande plattformsbegränsning och är inte blockerande. Mänsklig
interaktiv UI-granskning samt native/visuell Office-QA återstår till Task 5.
Task 4B committades därefter i `0b46d26`.

[Sprint 6-planen](plans/sprint6-planning-navigator.md) innehåller kontrakt,
acceptanskriterier, verifieringshistorik och dokumentationsavvikelser.
[Uppföljningsplanen](plans/sprint6-followup-tasks.md) beskriver de genomförda
Task 3B, 4A och 4B samt deras respektive reviewstatus.

## Alla sprintar

| Sprint | Tema | Status | Detaljer och historik |
|---|---|---|---|
| 1 | Stabil Riskanalys-demo | Avslutad | [Sprint 1](plans/sprint1-risk-analysis.md) |
| 2 | Stateful agent och robusta utkast | Avslutad | [Sprint 2](plans/sprint2-stateful-agent.md) |
| 3 | Tidplan | Avslutad | [Sprint 3](plans/sprint3-tidplan.md) |
| 4 | Mål och Leveranser | Avslutad | [Sprint 4](plans/sprint4-goals-deliverables.md) |
| 5 | WBS och WBS–Tidplan-samspel | Avslutad | [Sprint 5](plans/sprint5-wbs-tidplan.md) |
| 6 | Planeringsnavigator och demo readiness | Avslutad | [Sprint 6](plans/sprint6-planning-navigator.md) |
| 7 | Intressentanalys och Kommunikationsplan | Avslutad | [Sprint 7](plans/sprint7-stakeholder-communication.md) |
| 8 | Projektplan som planeringsstadiets slutartefakt | Task 0A redo för merge; nästa: Task 1 efter merge | [Sprint 8](plans/sprint8-projectplan.md) |

Sprint 5:s större verifieringshistorik finns separat i
[Task 8 inklusive 8A–8E](plans/sprint5-task8-verification.md) och
[Task 9 inklusive regression och AAR](plans/sprint5-task9-regression-aar.md).
Sprint 7 är avslutad enligt sin accepterade taskplan. Sprint 8 är öppnad med
[discoverykontrakt](plans/sprint8-projectplan-discovery.md) och
[detaljplan](plans/sprint8-projectplan.md). Task 0 är mergad/avslutad. Task
0A är genomförd och mergad; Task 1 är genomförd och squash-mergad i `fb24caf`;
Task 2 är nästa task.

## Läsordning och underhåll

1. Läs detta korta index för aktuell sprint, grind och nästa task.
2. Läs den länkade sprint-/taskplanen för uppgiftens scope, kontrakt och historik.
3. Läs relevant produkt- och arkitekturdokumentation enligt
   [COLLABORATION.md](COLLABORATION.md).

Indexet innehåller endast status och hänvisningar. Detaljer och historiska
testresultat hålls i `plans/`; de är inte nya testkörningar eller aktuella
arbetsinstruktioner. Vid statusändring uppdateras indexets sammanfattning och
berörd detaljplan tillsammans, med verifierbart underlag. Historiska kontrakt
och genomföranderapporter bevaras; oklara godkännanden markeras uttryckligen.
