# Samarbetsmodell

## Syfte

Detta dokument beskriver den återkommande arbetsformen mellan Produktägaren/verksamhetsrepresentanten, Chat, Work och Codex vid utvecklingen av Projektassistenten.

Syftet är att:

* göra utvecklingsprocessen konsekvent mellan sessioner;
* minska beroendet av konversationsminne;
* använda repot som permanent sanningskälla för projektets beslut och tillstånd;
* säkerställa att produkt- och arkitekturfrågor behandlas innan implementation;
* skapa en tydlig GitHub-baserad granskning mellan Codex implementation och produktacceptans;
* använda Codex-/Work-krediter där de ger störst nytta;
* stödja projektets lärandemål genom att viktiga resonemang och vägval förklaras.

Dokumentet kompletterar `AGENTS.md` men har ett annat ansvar.

`COLLABORATION.md` beskriver hur Produktägaren, Chat, Work och Codex samarbetar.

`AGENTS.md` beskriver hur arbete i kodrepot ska genomföras.

---

## Permanent sanningskälla och arbetskontext

GitHub-repot `petne797/ai-agent-kurs` är projektets permanenta sanningskälla för kod, produktregler, arkitektur, accepterade beslut, sprintstatus och arbetsregler.

Chat- och Work-sessioner är arbetskontext. De får använda tidigare konversationer som stöd, men ska inte behandla konversationsminne som en mer auktoritativ källa än aktuell dokumentation och faktiskt repo-tillstånd.

Codex arbetar mot det lokala repot i `C:\AI-Agent-Kurs`. GitHub är också den
gemensamma granskningsytan. Det lokala repot och GitHub ska hållas
synkroniserade genom task-branch-/PR-flödet som beskrivs nedan.

Om konversationsminne, dokumentation och faktisk kod motsäger varandra ska skillnaden identifieras och redovisas i stället för att en källa väljs tyst.

---

## Roller

### Produktägare / verksamhetsrepresentant

Produktägaren/verksamhetsrepresentanten ansvarar för:

* verksamhetsbehov, mål och prioriteringar;
* produktbeslut och verksamhetsregler;
* beslut om projektets riktning och omfattning;
* godkännande av viktiga produkt- och arkitekturval;
* interaktiv testning av gränssnitt och användarflöden;
* visuell och innehållsmässig bedömning av genererade artefakter;
* slutlig verksamhetsmässig och produktmässig bedömning;
* beslut om en task är produktmässigt accepterad och får mergas till `master`.

Produktägaren behöver inte själv översätta produktbehov till tekniska implementationstasks eller fatta normala tekniska detaljbeslut inom etablerad arkitektur.

När ett tekniskt vägval påverkar produktens beteende, arkitekturen, scope, framtida handlingsutrymme eller innebär en betydande trade-off ska Chat eller Work formulera en tydlig beslutspunkt och rekommendation för Produktägaren.

### Chat

Chat är Produktägarens huvudsakliga produkt- och beslutsstöd.

Chat används främst för:

* produkt- och kravdiskussion;
* verksamhetsanalys;
* prioritering och scope;
* analys av alternativ och konsekvenser;
* förklaring och lärande;
* sprint- och taskdiskussion;
* repo- och kodanalys via GitHub;
* teknisk kodreview av Codex implementation via GitHub.

Chat ansvarar för att:

* utgå från aktuell projektdokumentation när arbetet gäller Projektassistenten;
* hjälpa Produktägaren att formulera behov, beslut och avgränsningar;
* identifiera obeslutade produkt- eller arkitekturfrågor före implementation;
* skilja discovery och design från implementation;
* formulera avgränsade tasks och acceptanskriterier;
* rekommendera den billigaste Codex-modell och lägsta reasoning-nivå som med
  rimlig säkerhet klarar tasken;
* granska faktisk branch, PR, diff och commit mot scope, tester,
  dokumentation och etablerad arkitektur;
* skriva fokuserade korrigeringspromptar till samma Codex-session;
* bedöma Produktägarens manuella QA-resultat och rekommendera när tasken kan
  avslutas;
* rekommendera när ett konkret undantagsbehov motiverar Work;
* kunna förbereda en Codex-task enligt leveransstandarden nedan när tillräckligt underlag finns.

Chat ska inte hoppa direkt till implementation om en verklig produkt- eller arkitekturfråga först måste beslutas.

### Work

Work är ett undantagsverktyg, inte ett obligatoriskt steg i det normala
taskflödet. Work används endast när det finns ett konkret behov som Chat och
GitHub inte hanterar effektivt, exempelvis särskilt omfattande repoanalys,
tekniskt arbete som faktiskt kräver Work-miljön eller större tvärgående
analys där Work ger tydlig nytta.

Work ska inte användas enbart för att lägga till ytterligare en agentgranskning
av sådant som Chat kan analysera och reviewa via GitHub. När Work används
följer det samma krav på repoanknytning, källkontroll, scope och tydliga
beslutspunkter som Chat och Codex.

### Codex

Codex är den implementerande agenten för arbete i det faktiska kodrepot.

Codex ansvarar för att:

* läsa och följa `AGENTS.md` och Codex-bootstrapen i detta dokument;
* inspektera faktisk kod, tester, konfiguration och runtime path;
* verifiera antaganden mot repots faktiska tillstånd;
* implementera endast avtalat scope;
* lägga till eller uppdatera relevanta tester;
* genomföra proportionerlig verifiering;
* utföra automatiserad teknisk verifiering, inklusive relevanta unit-,
  integrations-, UI/state/lifecycle- och regressionstester;
* köra relevanta statiska kontroller, exempelvis compilation och
  `git diff --check`;
* uppdatera dokumentation när beteende eller arkitektur förändras;
* rapportera resultat, osäkerheter, verifiering och kvarvarande working-tree-förändringar;
* skapa och pusha en reviewbar version på taskens branch när implementationen
  är tekniskt redo och detta ingår i taskinstruktionen;
* korrigera review- och QA-fynd i samma Codex-session och på samma task-branch.

Codex får inte själv fatta nya produkt- eller arkitekturbeslut utanför det överenskomna scopet.

Om Codex upptäcker ett större produkt- eller arkitekturproblem än tasken förutsatte ska det rapporteras i stället för att lösas implicit.

Codex ska normalt inte använda krediter till interaktiv manuell UI-testning,
mänsklig UX-bedömning, visuell Office-QA, verksamhetsmässig innehållsbedömning
eller en separat Codex-review av sin egen implementation. Automatiserade
UI- och beteendetester ingår däremot fortsatt i Codex ansvar.

---

## Session bootstrap

En ny session ska kunna orientera sig från repot utan att Produktägaren behöver återberätta projektet eller arbetsmodellen.

Bootstrapen ska göras i början av en ny session som gäller utveckling av Projektassistenten och innan sessionen gör tekniska rekommendationer eller repoarbete.

### Chat-bootstrap

När en ny CHAT-session gäller Projektassistenten ska Chat:

1. läsa aktuell `COLLABORATION.md` från GitHub;
2. läsa det korta statusindexet `SPRINTS.md` för att identifiera aktuell sprint, grind och nästa task eller aktuellt arbetsområde;
3. läsa den länkade relevanta sprint- eller taskplanen under `plans/` när frågan beror på aktuell planering;
4. läsa `PRODUCT.md`, `ARCHITECTURE.md`, `ADR.md`, `ROADMAP.md` eller annan projektdokumentation när den aktuella frågan kräver det;
5. utgå från aktuell repo-dokumentation framför konversationsminne om de skiljer sig.

Chat behöver inte läsa hela repot för en enkel produktdiskussion. Bootstrapen ska vara proportionerlig mot uppgiften.

### Work-bootstrap

När en WORK-session undantagsvis används för Projektassistenten ska Work innan
tekniskt arbete:

1. läsa aktuell `COLLABORATION.md`;
2. läsa `AGENTS.md`;
3. läsa det korta statusindexet `SPRINTS.md`;
4. identifiera aktuell sprint, grind och nästa task eller det arbetsområde som ska analyseras;
5. läsa den länkade relevanta sprint-/taskplanen under `plans/`;
6. läsa relevant `PRODUCT.md`, `ARCHITECTURE.md`, `ADR.md`, `ROADMAP.md` och annan dokumentation som behövs;
7. verifiera aktuellt repo- och Git-läge när slutsatserna beror på faktisk kod eller working tree.

Work ska därefter kunna redovisa aktuell sprint/task, relevanta accepterade beslut, eventuella osäkerheter och rekommenderat nästa steg.

### Codex-bootstrap

`AGENTS.md` ska vara Codex automatiska ingång till arbetsmodellen. I början av en ny CODEX-session ska Codex:

1. verifiera att arbetskatalogen är rätt repo, normalt `C:\AI-Agent-Kurs`;
2. läsa `AGENTS.md`;
3. läsa `COLLABORATION.md` och följa denna Codex-bootstrap;
4. läsa det korta statusindexet `SPRINTS.md` och identifiera aktuell sprint, grind och nästa task när de är relevanta;
5. läsa den länkade relevanta sprint-/taskplanen under `plans/` och den projektdokumentation som tasken kräver;
6. kontrollera `git status --short` innan filer ändras;
7. stoppa och rapportera om arbetskatalog, repo-status eller dokumentation inte stämmer med taskens antaganden.

Codex ska inte behöva få permanenta repo- och samarbetsregler duplicerade i varje taskprompt.

---

## Session management och organisering

Projektets sessioner organiseras enligt följande standard:

* **GitHub/repo = permanent sanningskälla.** Projektets långsiktiga produkt-, arkitektur-, sprint- och samarbetskontext ska finnas i repot när den behöver överleva sessioner.
* **ChatGPT-projekt = gemensam arbetsyta.** ChatGPT-projektet `Projektassistenten` samlar CHAT- och WORK-sessioner och gemensamma projektinstruktioner, men ersätter inte repot som sanningskälla.
* **Lokalt Codex-projekt = kodrepot.** Codex-sessioner som ska läsa eller ändra repot ska skapas från det lokala Codex-projekt som pekar på `C:\AI-Agent-Kurs`.
* **Avsnitt = en sprint och gemensam organiseringsnivå.** Varje sprint får ett eget avsnitt som samlar de CHAT-, WORK- och CODEX-sessioner som hör till sprinten.
* **CHAT = normalt en session per sprint.** CHAT-sessionen används för produktdiskussion, beslut, planering och löpande samordning på sprintnivå.
* **WORK = undantag.** En WORK-session skapas bara för ett konkret arbetsområde
  där Work-miljön ger tydlig nytta utöver Chat och GitHub.
* **CODEX = en session per task.** Varje avgränsad implementationstask eller annan repotask utförs normalt i en separat CODEX-session. Korrigeringar efter review fortsätter i samma session så länge de tillhör samma task.
* **PR = taskens reviewyta.** En task-branch och en lättviktig PR används för
  implementation, review och korrigeringar.
* **Commit på `master` = en godkänd task.** Reviewbranchen kan innehålla flera
  iterationscommits, men squash merge ger normalt en slutlig commit per
  accepterad task på `master`.

### Visning i ChatGPT- och Codex-läget

ChatGPT-projekt och lokala Codex-projekt är olika typer av objekt och har olika ansvar.

* ChatGPT-projektet `Projektassistenten` ger CHAT och WORK gemensam arbetskontext och projektinstruktioner.
* Det lokala Codex-projektet ger Codex tillgång till rätt repo och arbetskatalog.
* Sprintavsnittet är den gemensamma organisatoriska nivån för CHAT, WORK och CODEX.
* Att en session syns i rätt sprintavsnitt bevisar inte att den har rätt arbetskatalog. Organisering och exekveringskontext ska kontrolleras var för sig.

### Kontroll av Codex arbetskatalog

Innan en ny CODEX-session får ändra eller verifiera repot ska följande kontrolleras:

* sessionen är skapad från det lokala Codex-projekt som pekar på `C:\AI-Agent-Kurs`;
* sessionens faktiska arbetskatalog är `C:\AI-Agent-Kurs`;
* repots förväntade filer, exempelvis `SPRINTS.md`, `ROADMAP.md`, `AGENTS.md` och `agent/`, är tillgängliga;
* `git status --short` avser Projektassistentens repo och inte ett annat lokalt projekt;
* sessionen placeras därefter i rätt sprintavsnitt utan att projektkopplingen ändras.

Kontrollen ska upprepas efter omstart av datorn eller Codex-appen och när en session skapas, forkas, flyttas eller återupptas från ett annat projekt. Om arbetskatalogen är tom eller saknar förväntade filer ska arbetet stoppas innan några filer ändras.

### Namnstandard

Namn ska göra sprint, sessionstyp och scope tydliga även i sökresultat och listor där avsnittet inte syns.

* **ChatGPT-projekt:** `Projektassistenten`.
* **Lokalt Codex-projekt:** ett projekt som pekar på `C:\AI-Agent-Kurs`, normalt visat som `AI-Agent-Kurs`.
* **Sprintavsnitt:** `SNN – <sprinttema>`, exempelvis `S05 – WBS och Tidplan`.
* **CHAT-session:** `SNN — CHAT — <sprinttema eller huvudsakligt samtalsområde>`, exempelvis `S05 — CHAT — WBS och Tidplan`.
* **WORK-session:** `SNN — WORK — <arbetsområde>`, exempelvis `S05 — WORK — Repo review`.
* **CODEX-session:** `SNN TNN — CODEX — <avgränsad task>`, exempelvis `S05 T01 — CODEX — WBS domain + codec`.

`SNN` är sprintnumret med två siffror och `TNN` är taskens löpnummer inom sprinten. En korrigering efter review behåller samma sessionsnamn eftersom den fortfarande tillhör samma task.

Äldre sessioner från Sprint 1–4 kan samlas i ett gemensamt avsnitt med namnet `S01-04 Gamla sessioner`.

### Sessionsbyte och överlämning

* **Ny sprint innebär nytt avsnitt och ny CHAT-session.**
* **Ny sprint innebär inte automatiskt en WORK-session.** Work används endast
  vid ett konkret undantagsbehov.
* **Ny task innebär ny CODEX-session.** Nästa självständiga task ska inte fortsätta i föregående tasks CODEX-session.
* **Samma task behåller samma CODEX-session.** Frågor, verifiering och korrigeringar efter review fortsätter i samma session så länge taskens mål och scope är oförändrade.
* **Förändrat scope kan kräva ny task och session.** Om arbetet växer till ett självständigt mål eller kräver ett nytt produkt- eller arkitekturbeslut ska det normalt avgränsas som en ny task.

En överlämning skapas när nästa session inte rimligen kan förstå arbetsläget enbart från permanent projektdokumentation och repots faktiska tillstånd.

En överlämning ska vid behov innehålla:

* sprint- och taskidentitet;
* mål, beslutat scope och out of scope;
* accepterade produkt- och arkitekturbeslut;
* genomfört arbete och aktuellt resultat;
* relevanta filer, dokument och commits;
* verifiering och testresultat;
* kvarvarande problem, osäkerheter och beslutspunkter;
* working-tree- och commitstatus när repoarbete berörs;
* rekommenderat nästa steg.

Överlämningen är arbetskontext, inte en ny permanent sanningskälla. Beslut och tillstånd som ska gälla långsiktigt ska också finnas i relevant projektdokumentation.

---

## Standardflöde för en task

### 1. Task identifieras

Produktägaren identifierar tillsammans med Chat eller Work nästa avgränsade task.

En task kan exempelvis vara discovery, produkt- eller domändesign, arkitekturanalys, implementation, test/verifiering, dokumentationsarbete eller teknisk skuld.

### 2. Analys och beslut

Chat analyserar nuläget på den nivå som tasken kräver och läser faktisk kod
och dokumentation via GitHub när slutsatsen beror på implementationen. Work
används bara när ett konkret behov motiverar undantaget.

Om analysen visar en viktig obeslutad produkt- eller arkitekturfråga ska implementation stoppas vid beslutspunkten. Chat eller Work ska redovisa alternativ, rekommendation, konsekvenser och vilket beslut som behövs från Produktägaren.

### 3. Rekommendation

Inför en implementationstask ska Chat eller Work normalt redovisa:

* **Bedömning och resonemang** – vad problemet är och vad nuläget innebär;
* **Rekommenderad lösning** – vad som bör göras;
* **Motivering** – varför lösningen är lämplig;
* **Arkitekturpåverkan** – vilka befintliga gränser eller principer som berörs;
* **Out of scope** – vad som uttryckligen inte ska göras i tasken.

### 4. Produktägaren godkänner riktningen

Codex-paketet ska inte levereras som startklar implementation om en verklig beslutspunkt fortfarande är öppen.

När riktning, scope och viktiga beslut är tillräckligt tydliga kan Chat eller Work förbereda överlämningen till Codex.

### 5. Chat levererar Codex-paketet

När en ny task ska startas i en ny CODEX-session ska Chat eller Work alltid avsluta överlämningen med följande tre delar i denna ordning:

#### 1. Rekommenderad Codex-modell och reasoning

Ange vilken **för tillfället tillgänglig Codex-modell** och reasoning-nivå som
rekommenderas och ge en mycket kort motivering.

Grundprincipen är att välja den billigaste modellen och lägsta reasoning-nivån
som med rimlig säkerhet klarar tasken. En dyrare eller mer resurskrävande
modell ska rekommenderas först när taskens faktiska komplexitet motiverar det.
En stor men tydligt specificerad implementation är inte automatiskt ett skäl
att välja den dyraste modellen eller `high` reasoning.

Aktuell arbetshierarki är:

* **GPT-5.6 Luna** – enkla, välavgränsade ändringar och repetitiva tester;
* **GPT-5.6 Terra** – normalt förstahandsval för vanlig utveckling över flera
  filer inom etablerad arkitektur;
* **GPT-5.6 Sol** – verklig teknisk komplexitet, svårare felsökning eller
  betydande tvärgående påverkan;
* **GPT-6 Astra** – uttryckligt motiverat undantag för de svåraste problemen
  eller när en billigare modell visat sig otillräcklig.

Eskalering sker vid behov i riktningen Luna → Terra → Sol → Astra. Samma
sparsamhetsprincip gäller reasoning: börja på lägsta rimliga nivå och höj bara
för att tasken kräver djupare problemlösning. Om produkten stödjer modellbyte
kan samma Codex-session fortsätta med en starkare modell utan att tasken
startas om.

Modellutbud, relativa kreditkostnader, priser och kvoter förändras. Chat ska
kontrollera aktuell officiell OpenAI-dokumentation när rekommendationen är
viktig eller uppgifterna kan ha ändrats. Permanent projektdokumentation ska
inte innehålla uppskattade meddelandekvoter eller fasta prisuppgifter.

#### 2. Första prompten

Ge en komplett första prompt som kan kopieras direkt till en ny CODEX-session.

Prompten ska vara självständig för den aktuella tasken men ska inte duplicera permanenta regler som redan finns i `COLLABORATION.md` eller `AGENTS.md`.

Den ska hänvisa till relevant permanent dokumentation och taskplan. Codex ska
inte rutinmässigt instrueras att läsa hela dokumentationsmängden eller hela
repot när tasken är smalare.

Den ska normalt innehålla när det är relevant:

* task och mål;
* relevant nuläge eller bakgrund;
* scope;
* out of scope;
* redan fattade produkt- och arkitekturbeslut;
* acceptanskriterier;
* krav på proportionerliga fokuserade tester och relevant regression;
* dokumentationskrav;
* task-branch, reviewbar commit/push och rapportering av branch, PR och SHA;
* vad Codex ska rapportera tillbaka.

#### 3. Sessionsnamn

Ange ett färdigt sessionsnamn enligt namnstandarden:

`SNN TNN — CODEX — <avgränsad task>`

De tre delarna fungerar som signal att analys-/beslutsfasen är klar och att en ny Codex-session kan startas.

### 6. Produktägaren startar Codex-sessionen

Produktägaren skapar taskens CODEX-session från det lokala projekt som pekar på `C:\AI-Agent-Kurs`, verifierar arbetskatalogen och placerar sessionen i sprintens avsnitt. Därefter ges den första prompten till Codex.

### 7. Codex arbetar i repot

Codex följer bootstrapen och `AGENTS.md`, skapar task-branchen, inspekterar,
implementerar, kör automatiserad teknisk verifiering och rapporterar
resultatet. När implementationen är tekniskt redo gör Codex en reviewbar
commit, pushar task-branchen och öppnar eller uppdaterar en lättviktig PR mot
`master`.

### 8. Chat genomför teknisk review via GitHub

Chat läser den faktiska PR-diffen och den exakta branch-tip/SHA som Codex har
rapporterat. Codex implementationsrapport är underlag men inte sanningskälla.

Review ska i första hand verifiera:

* faktisk diff och ändrade filer;
* taskens mål, scope och acceptanskriterier;
* test- och verifieringsresultat;
* relevant produktmodell och arkitektur;
* dokumentationsändringar;
* working-tree- och commitstatus när det är relevant.

Codex rapport ska jämföras med det faktiska resultatet och ska inte ensam användas som bevis på att tasken är korrekt.

### 9. Korrigering och omreview

Om implementationen har brister skriver Chat en fokuserad korrigeringsprompt.
Korrigeringen fortsätter i samma CODEX-session, task-branch och PR så länge
mål och scope är oförändrade. Codex testar, committar och pushar en ny
reviewbar version. Chat granskar den nya branch-tip/SHA:n.

Separat oberoende Codex-review är ett uttryckligt undantag för ovanligt
riskfyllda eller komplexa förändringar, inte ett normalt steg.

### 10. Produktägaren genomför nödvändig produkt-QA

När tasken kräver det testar Produktägaren det faktiska användarflödet och
bedömer UX, texter, visuellt resultat, Office-artefakter och verksamhetsmässig
kvalitet. Kodrelaterade fynd går tillbaka till samma Codex-session och samma
PR. Alla tasks behöver inte manuell QA; nivån ska vara proportionerlig.

### 11. Review- och acceptansgrind

När Chat har godkänt kodreviewn och Produktägaren har godkänt nödvändig
produkt-QA fattar Produktägaren det slutliga beslutet om tasken får mergas.

### 12. Squash merge till `master`

PR:n squash-mergas till `master`, så att taskens reviewiterationer blir en
slutlig taskcommit. Task-branchen tas normalt bort och det lokala repot
synkroniseras med `origin/master`.

### 13. Nästa task

Nästa task påbörjas först när den föregående är färdig eller uttryckligen har avbrutits eller skjutits upp. Nästa task får normalt en ny CODEX-session.

---

## Krav på Codex-promptar

En Codex-prompt ska beskriva taskspecifikt mål och viktiga gränser utan att återberätta hela projektets permanenta arbetsmodell.

Använd i första hand hänvisning till repo-dokumentationen i stället för att kopiera långlivade regler till varje prompt.

Prompten ska ge Codex tillräckligt handlingsutrymme för att inspektera repot och föreslå eller implementera den minsta lösning som uppfyller det beslutade kontraktet.

Prompten ska inte detaljstyra implementationen mer än vad som behövs för scope, produktregler, arkitekturgränser, säkerhet eller verifiering.

---

## Review gate

Passerande tester innebär inte automatiskt att en task är färdig.

Vid review ska Chat även bedöma om:

* implementationen följer avtalat scope;
* lösningen följer produktmodellen;
* arkitekturgränserna respekteras;
* human-in-the-loop-principen är bevarad;
* en andra sanningskälla inte har införts;
* en parallell lifecycle eller runtime path inte har skapats;
* lösningen är mer generell eller komplex än det konkreta behovet motiverar;
* befintliga återanvändbara gränser har använts när de passar;
* testerna verifierar rätt kontrakt och inte endast implementationens interna detaljer;
* failure paths och state mutation är säkra där det är relevant;
* relevant dokumentation har uppdaterats;
* orelaterade ändringar har hållits utanför tasken.

Kodreview och produkt-QA är separata grindar. Vid Office-artefakter kan
produkt-QA kräva normal öppning i Microsoft Word eller Excel och mänsklig
visuell bedömning innan tasken betraktas som färdig.

---

## QA-modell

Verifieringen har tre tydliga nivåer. Alla tasks behöver inte alla tre;
omfattningen ska vara proportionerlig mot risk och innehåll.

### Automatiserad teknisk verifiering

**Ansvar: Codex.** Exempel är unit- och integrationstester, automatiserade
UI/state/lifecycle-tester, regression, codec-, schema-, grounding- och
invarianttester, compilation och `git diff --check`.

Fokuserade tester körs under implementationen. Relevant regression körs när
tasken är tekniskt färdig. Full supported regression körs när taskens scope,
risk eller sprintens QA-grind motiverar det. Samma breda verifiering ska inte
upprepas utan konkret anledning.

### Kodreview och arkitekturgranskning

**Ansvar: Chat via GitHub.** Review omfattar faktisk diff och SHA, scope,
arkitektur, säkerhet, informationsauktoritet, lifecycle, komplexitet,
testtäckning, testkvalitet och dokumentationsöverensstämmelse.

### Interaktiv och produktmässig QA

**Ansvar: Produktägaren.** Exempel är faktisk användning av Streamlit eller
annat UI, användarflöden, texter och begriplighet, visuell kontroll,
Word-/Excel-artefakter, verksamhetsmässig kvalitet och slutlig
produktacceptans.

---

## Git-arbetsflöde

Den enklaste robusta standarden är en task-branch med en lättviktig GitHub-PR
och squash merge:

* en task i taget;
* `master` ska motsvara senast accepterade och mergade kod;
* varje ny task utgår från synkroniserad `master` och får en egen branch med
  ett kort namn som identifierar sprint, task och scope;
* Codex får göra en eller flera reviewbara commits på task-branchen och pusha
  dem enligt taskinstruktionen;
* en lättviktig PR mot `master` är taskens gemensamma reviewyta;
* Chat reviewar exakt branch-tip/SHA via GitHub;
* reviewkorrigeringar fortsätter på samma branch, i samma PR och i samma
  Codex-session;
* efter godkänd kodreview och nödvändig produkt-QA squash-mergas PR:n till
  `master`, vilket ger en slutlig commit per accepterad task;
* task-branchen tas normalt bort efter merge;
* orelaterade lokala ändringar lämnas orörda;
* en ny task ska normalt inte påbörjas med orelaterade ocommittade ändringar från den föregående tasken.

Ingen utvecklingstask ska pusha ogranskad kod direkt till `master`. PR:n ska
vara lättviktig: projektet kräver inte ett enterprise-liknande branch- eller
godkännandeflöde, flera formella reviewers eller särskild branch protection
för att tillämpa modellen.

En commit eller grön testkörning är inte i sig bevis på att implementationen
är produktmässigt accepterad.

---

## Förhållande till projektdokumentationen

Projektets olika dokument har olika ansvar.

### Projektdokumentation i kodrepot

Exempel:

* `PRODUCT.md` – produktmodell och produktregler;
* `ARCHITECTURE.md` – nuvarande systemarkitektur;
* `ADR.md` – accepterade arkitekturbeslut;
* `ROADMAP.md` – långsiktig prioritering;
* `SPRINTS.md` – kort statusindex för alla sprintar, aktuell grind och nästa task;
* `plans/` – detaljerade sprint-/taskplaner och bevarad genomförandehistorik;
* `AGENTS.md` – regler för hur arbete i repot ska genomföras;
* `IDEAS.md` – ännu inte prioriterade idéer och teknisk skuld;
* `COLLABORATION.md` – arbetsformen mellan Produktägaren, Chat, Work och Codex.

Dessa dokument ska uppdateras tillsammans med implementationen när deras innehåll påverkas.

### Ansvarsfördelning mellan dokumenten

```text
Produkt och arkitektur
→ projektdokumentationen i repot

Repoarbete
→ AGENTS.md

Produktägare–Chat–Work–Codex-processen
→ COLLABORATION.md
```

`COLLABORATION.md` ligger i repot och ska kunna läsas av Chat, Work och Codex. Det är den gemensamma källan för rollfördelning, bootstrap och arbetsflöde.

---

## Övergång till denna arbetsmodell

Task 4B i Sprint 6 avslutades enligt den tidigare modellen i commit
`0b46d26`. Den kreditoptimerade Chat–Codex–Produktägare-modellen och
task-branch-/PR-flödet gäller för självständiga tasks som startas efter att
denna dokumentationsversion har pushats till `origin/master`. Sprint 6 Task 5
är den första planerade tasken enligt den nya modellen.

Historiska taskbeskrivningar och verifieringsrapporter ska behålla det
arbetssätt som faktiskt användes när de genomfördes.

---

## Rekommenderad projektinstruktion för ChatGPT-projektet

ChatGPT-projektet `Projektassistenten` bör ha en kort beständig instruktion som pekar på repo-versionen av detta dokument i stället för att duplicera hela arbetsmodellen.

Rekommenderad instruktion:

> Projektets permanenta sanningskälla är GitHub-repot `petne797/ai-agent-kurs`. När en ny Chat- eller Work-session gäller utveckling av Projektassistenten ska du först läsa aktuell `COLLABORATION.md` från repot och följa roll- och bootstrapreglerna där. Läs därefter den projektdokumentation som är relevant för uppgiften. Utgå från aktuell repo-dokumentation och faktiskt repo-tillstånd framför konversationsminne när de skiljer sig.
