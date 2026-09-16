# Projektassistenten

## Vision

Projektassistenten är en AI-agent som hjälper projektledare att planera, genomföra och avsluta projekt enligt UIT:s projektmodell och PPS.

Målet är att minska den administrativa arbetsbördan och samtidigt höja kvaliteten på projektets styrning och dokumentation.

Projektassistenten ska fungera som ett kvalificerat stöd till projektledaren. Den analyserar projektets information, skapar arbetsutkast, identifierar risker, avvikelser och informationsluckor samt föreslår förbättringar.

Projektassistenten ersätter aldrig projektledarens, projektägarens eller styrgruppens ansvar eller beslut.

---

# Grundprinciper

## Human in the loop

Alla dokument som skapas eller ändras av agenten är arbetsutkast tills projektledaren uttryckligen har godkänt dem.

Projektledaren äger beslutet att godkänna en artefakt för export. Agenten ska
identifiera informationsluckor, visa öppna frågor och rekommendera
kompletteringar, men öppna frågor är normalt kvalitetsinformation och inte ett
approval-veto. Godkännande betyder att artefakten är godkänd för export, inte
att den är fullständig eller fri från informationsluckor. Hårda regler för
schema, integritet, projektidentitet, grounding och andra absoluta invariants
kan fortfarande blockera godkännande.

Det normala arbetsflödet är:

Projektledaren säkerställer att relevanta underlag finns på rätt plats

↓

Projektledaren initierar en uppgift

↓

Agenten analyserar relevant underlag

↓

Agenten skapar eller uppdaterar ett arbetsutkast

↓

Projektledaren granskar

↓

Projektledaren ger feedback

↓

Agenten reviderar utkastet

↓

Projektledaren godkänner

↓

Agenten genererar dokumentet

↓

Projektledaren verifierar dokumentet och beslutar om det ska placeras i projektets ordinarie dokumentstruktur.

Agenten skapar aldrig projektbeslut.

Agenten får:

- analysera
- sammanfatta
- föreslå formuleringar
- identifiera risker
- identifiera konflikter mellan dokument
- identifiera informationsluckor
- föreslå aktiviteter och åtgärder
- ställa frågor när underlaget är otillräckligt

Agenten får inte:

- fatta projektbeslut
- hitta på projektfakta
- ändra beslut eller fastställda mål utan stöd i nya beslut
- ändra styrande dokument utan projektledarens godkännande
- ersätta projektledaren, projektägaren eller styrgruppen

---

# Central produktmodell

Projektassistentens beteende bestäms av kombinationen:

```text
Artefakt
+
Operation
+
Projektfas
```

Samma artefakt kan därför behandlas på olika sätt beroende på var projektet befinner sig i sin livscykel.

Exempel:

```text
Riskanalys + CREATE + Planering
→ skapa ett första arbetsutkast med relativt stor kreativ frihet
```

```text
Riskanalys + REFINE + Planering
→ utveckla, komplettera och vid behov omstrukturera arbetsutkastet
```

```text
Riskanalys + UPDATE + Genomförande
→ göra begränsade och spårbara ändringar baserade på ny projektinformation
```

Det är alltså inte artefakten ensam som bestämmer agentens beteende.

---

# Projektets informationsstruktur

Projektassistenten använder i första hand projektets mappstruktur för att förstå vilken roll informationen har.

Mapparna representerar informationsdomäner, projektfaser eller verkliga projekthändelser.

Filnamn beskriver i första hand dokumenttypen.

## Princip för mappar och filer

Mappar ska användas när de representerar något verkligt i projektet, exempelvis:

- en informationsdomän
- en projektfas
- ett styrgruppsmöte
- en workshop eller annan sammanhållen aktivitet
- projektledarens AI-Input

Dokumenttyper ska normalt representeras av filnamnet.

Exempel:

```text
Styrgrupp/
    2026-10-15 SG03/
        Statusrapport.docx
        Presentation.pptx
        Minnesanteckningar.docx
        Beslut.docx
```

Inte:

```text
Styrgrupp/
    2026-10-15 SG03/
        Statusrapport/
        Presentation/
        Beslut/
```

Projektets mappstruktur ska spegla projektets verkliga arbete, inte systemets interna implementation.

---

# Kanonisk projektstruktur

En projektmapp bör i grunden kunna se ut så här:

```text
Projekt/
│
├── metadata.json
│
├── Styrdokument/
│   ├── Projektdirektiv.docx
│   ├── Projektplan.docx
│   ├── Riskanalys.xlsx
│   ├── Tidplan.xlsx
│   ├── WBS.xlsx
│   ├── Intressentanalys.xlsx
│   └── Kommunikationsplan.xlsx
│
├── Projektplanering/
│   ├── ... aktuella planeringsdokument ...
│   └── AI-Input/
│
├── Styrgrupp/
│   ├── 2026-09-15 SG01/
│   │   ├── Statusrapport.docx
│   │   ├── Presentation.pptx
│   │   ├── Minnesanteckningar.docx
│   │   └── Beslut.docx
│   │
│   └── 2026-10-20 SG02/
│       └── ...
│
├── Projektgenomförande/
│   └── AI-Input/
│
├── Projektavslut/
│   └── AI-Input/
│
├── AI-utkast/
│
└── Export/
```

Under Planering är `Styrdokument/**` och `Projektplanering/**` aktiva källdomäner. Alla läsbara dokument där är legitima källor enligt informationsauktoriteten, oavsett om de har skapats av Projektassistenten, manuellt eller av ett annat verktyg.

Projektledaren får skapa andra mappar och strukturer för sitt eget arbete. Material i sådana övriga mappar ska inte automatiskt tolkas som aktuellt AI-underlag enbart för att det finns under projektroten.

---

# AI-Input

## Syfte

`AI-Input` är projektledarens explicita arbetsyta mot Projektassistenten för material som särskilt ska tillföras agentens arbete.

Under Planering är AI-Input inte den enda vägen för manuellt arbetsmaterial. Alla läsbara dokument i `Projektplanering/**` är legitima aktuella planeringskällor, och `Projektplanering/AI-Input/**` ingår i denna aktiva källdomän som en tydlig plats för extra material.

Projektledaren kan samtidigt behålla annan egen dokumentstruktur för exempelvis:

- arbetsanteckningar
- möten
- ekonomi
- tekniska analyser
- leverantörsdokument
- e-post
- tester
- workshops

När material utanför en aktiv källdomän ska påverka agentens planeringsarbete kan projektledaren lägga en kopia i `Projektplanering` eller dess `AI-Input`.

Exempel:

```text
Projektplanering/
    AI-Input/
        Workshop målbild 2026-09-12/
            Whiteboard.jpg
            Anteckningar.docx
            Transkribering.docx

        Egna tankar inför riskworkshop.md
```

eller, i en senare projektfas där det uttryckligen valda källkontraktet tillåter det:

```text
Projektgenomförande/
    AI-Input/
        Minnesanteckningar projektgrupp 2026-10-11.docx
        Leverantörens nya tidplan.xlsx
        Analys av migreringsproblem.docx
```

Exakt källkontrakt för Genomförande och Avslut beslutas per fas och operation. Ett dokument blir inte mer styrande eller sant enbart för att det ligger i AI-Input.

Det gör beteendet:

- transparent
- kontrollerbart
- mindre känsligt för dokumentnamn
- mindre beroende av metadata
- mindre känsligt för gamla eller inaktuella arbetsdokument utanför aktiva källdomäner

Projektledaren styr alltså agentens aktuella arbetsunderlag genom att placera material i rätt aktiva informationsdomän och kan använda AI-Input som en explicit extra ingång.

---

# Informationsauktoritet

Olika informationskällor har olika betydelse.

Agenten ska skilja mellan:

- styrning och beslut
- gällande plan
- ny information om vad som faktiskt hänt
- arbetsmaterial
- AI-genererat innehåll
- extern metodik

Vid motstridig information ska agenten inte försöka jämna ut motsägelsen.

Den ska identifiera och redovisa den.

Placering i `Styrdokument/**` betyder att dokumentet är aktuellt, fastställt och styrande. Endast den gällande versionen bör ligga i denna aktiva domän; versionshistorik hanteras exempelvis av SharePoint eller utanför den aktiva källdomänen.

När både fastställd Projektplan och Projektdirektiv finns är Projektplanen primär beskrivning av den aktuella planen, medan Projektdirektivet fortsatt är styrande bakgrund för uppdrag, syfte, mandat och sådant som inte har ersatts av Projektplanen.

Som generell princip väger:

```text
Gällande styrdokument
↓
Formella beslut när den aktuella operationen behandlar dem som förändringsauktoritet
↓
Verifierad aktuell projektinformation
↓
Planerings- och arbetsmaterial, inklusive AI-Input
↓
Dokument från liknande projekt inom UU
↓
Extern generell kunskap
```

Den exakta källprioriteringen kan variera beroende på artefakt, operation och fas. Källinkludering och informationsauktoritet är separata frågor: ett dokument kan vara legitimt underlag utan att få skriva över motstridig styrning.

---

# Planering

## Syfte

Bygga upp projektets planeringsunderlag och de styrande dokument som behövs inför projektets genomförande.

Projektdirektivet är det huvudsakliga styrande dokumentet i början av planeringsfasen. När en fastställd Projektplan senare finns blir den primär beskrivning av den aktuella planen inom `Styrdokument`.

Agenten får under planeringen arbeta relativt kreativt.

Den får exempelvis:

- skapa första utkast
- identifiera och föreslå nya risker
- formulera mål
- föreslå leveranser
- föreslå aktiviteter
- skapa och förändra struktur
- kombinera information från flera planeringsartefakter
- utveckla dokument utifrån workshops och annat nytt underlag

## Källkontrakt

Under Planering gäller följande generella källkontrakt:

```text
Styrdokument/**
    alla läsbara dokument är legitima och styrande källor
```

```text
Projektplanering/**
    alla läsbara dokument är legitima aktuella planeringskällor
```

```text
Projektplanering/AI-Input/**
    explicit tillfört extra arbetsmaterial inom samma planeringsdomän
```

Det gäller oavsett om dokumenten har skapats av Projektassistenten, manuellt eller av annat verktyg. Artefaktspecifika source rules får prioritera relevans men ska inte göra kända dokumenttyper till en exklusiv allowlist för `Projektplanering`.

PPS-metodik, UIT:s mallar och vid behov relevanta tidigare projekt får användas som metod- och inspirationsstöd men blir inte projektfakta av den anledningen.

## Artefakter

Planeringsartefakterna har en logisk best-practice-relation:

```text
Mål → Leveranser → WBS → Tidplan
```

Varje artefakt kan ge underlag till nästa, men kedjan är inte en obligatorisk
arbetsordning. Planeringsarbetet är iterativt och händelsestyrt: tillgängliga
källor, möten och workshops, medverkande personer, beslut,
informationsluckor och förändrade förutsättningar avgör vad som kan skapas
eller förfinas härnäst. Det ska därför gå att börja med artefakten som har
tillräckligt underlag och senare använda mänskligt accepterade normala
projektartefakter för att förbättra andra. Ett arbetsutkast som endast är
godkänt för export är ännu inte en sådan projektkälla.

Artefakterna är fortsatt separata arbetsutkast med egen domänmodell och egen
presentation; kedjan innebär inte att en Office-mall, ett exportformat eller
ett AI-utkast blir den gemensamma sanningskällan. AI-utkast används inte som
normala projektkällor.

### Planeringsnavigator

Planeringsnavigatorns produktkontrakt och första slice är implementerade. Den
uttryckliga rådgivande funktionen `Rekommendera nästa steg` omfattar
Riskanalys, Mål, Leveranser, WBS, Tidplan, Intressentanalys och
Kommunikationsplan.
Navigatorn får inte automatiskt skapa, revidera, godkänna eller exportera en
artefakt och får inte mutera projektinformation, DraftStore eller persistens.
Projektledaren väljer alltid en separat explicit funktion efter att ha läst
rådet.

Navigatorn använder två tydligt separerade bedömningar:

1. Deterministisk kod avgör vilka artefakter som är kandidater, vilken action
   varje kandidat har, vilka blockerare som gäller och vilken hård
   prioritetsnivå kandidaten tillhör.
2. AI får rangordna kandidater inom samma prioritetsnivå och komplettera med
   motivering, semantiska luckor, osäkerheter och utredningsfrågor.

AI får aldrig skapa eller ta bort en kandidat, ändra dess action eller
prioritetsnivå, passera en blockerare, rangordna över en hård prioritetsgräns
eller exekvera en funktion. Ett ogiltigt AI-resultat kastas i sin helhet och
ersätts av den stabila deterministiska fallbackordningen.

#### Mjuk ordning och fallback

Den befintliga best-practice-relationen förblir mjuk:

```text
Mål → Leveranser → WBS → Tidplan
Intressentanalys ───────────────┐
Tidplan ────────────────────────┴→ Kommunikationsplan
```

Riskanalys är ett tvärgående spår utan hård föregångare. En senare artefakt
kan vara kandidat när dess egna legitima källor räcker, även om en tidigare
artefakt saknas. Den får då en lägre deterministisk prioritetsnivå än en
motsvarande kandidat vars mjuka föregångare redan finns som normala
projektartefakter. Intressentanalys har ingen hård eller mjuk föregångare i
Navigatorpolicyn. Kommunikationsplanens två starka mjuka föregångare är
Intressentanalys och Tidplan. CREATE är fortsatt tillåten utan båda när eget
legitimt underlag räcker; minst en `UNIQUE` mjuk föregångare höjer kandidaten
från nivå 3 till nivå 2.

Inom samma prioritetsnivå används följande fallback och tie-break när giltig
AI-rangordning saknas:

```text
Mål → Riskanalys → Intressentanalys → Leveranser → WBS → Tidplan → Kommunikationsplan
```

Fallbackordningen är inte alltid den slutliga rådgivande rangordningen; AI får
rangordna om kandidater inom samma nivå.

#### Normal projektartefakt och arbetsutkast

Navigatorn håller normal projektartefakt och DraftStore-status åtskilda.
Normal projektartefakt har lägena `ABSENT`, `UNIQUE`, `AMBIGUOUS` och
`INVALID`. DraftStore har separat lägena `MISSING`, `AI_DRAFT`, `APPROVED` och
`INVALID`.

En normal projektartefakt är en legitim projektfil och benämns aldrig
`approved`. `APPROVED` betyder endast att ett AI-arbetsutkast har godkänts för
export. Ett `APPROVED` utkast utan en normal projektartefakt uppfyller därför
inte en mjuk föregångarrelation och får inte användas som projektkälla.

En normal fil räknas som den aktuella artefakten endast när den kan lösas
entydigt under aktiv kanonisk projektrot från ett aktuellt dokumentindex. Den
ska ha rätt dokumenttyp och någon av de exakta kombinationerna
`STYRANDE/Gällande` eller `PROJECT_ARTIFACT/Projektunderlag`.
`STYRANDE/Gällande` prioriteras. Exakt en läsbar och verifierbar kandidat på
högsta tillgängliga nivå ger `UNIQUE`. Flera kandidater där ger `AMBIGUOUS`.
En oläsbar eller overifierbar kandidat på den högsta nivån ger `INVALID`;
Navigatorn får inte bortse från den och deklarera en annan fil `UNIQUE`.
`AMBIGUOUS` och `INVALID` leder till `INVESTIGATE` och aldrig till ett
dubblettskapande.

Dokumenttyperna är `Riskanalys`, `Måldokument`, `Leveransdokument`, `WBS`,
`Tidplan`, `Intressentanalys` och `Kommunikationsplan` för respektive
produktartefakt. De två nya XLSX-artefakterna strukturverifieras genom sina
befintliga Office-/groundinggränser. Kommunikationsplanens deklarerade
STK-, TPG- och aktivitetsrelationer samt Huvudaktivitetsordning verifieras mot
aktuella unika normala källor. AI-Input, AI-utkast, Export och andra
runtimeytor kan aldrig vara en normal projektartefakt.

#### Rekommenderade actions

Den första slicens Navigatoractions är:

- `CREATE`: använd den separata funktionen Skapa;
- `REVIEW`: använd Visa och granska ett befintligt `AI_DRAFT`;
- `INVESTIGATE`: utred underlag, tvetydighet eller blockerare;
- `NO_ACTION`: inget körbart nästa planeringssteg.

REFINE är det interna planeringsbegreppet bakom den användarnära funktionen
Revidera. UPDATE är reserverat för genomförandefasen. Navigatorn inför inte
Förfina som ett konkurrerande UI-begrepp.

Ett `AI_DRAFT` ger alltid REVIEW. Efter visningen väljer projektledaren själv
Revidera, Godkänn, Utred eller ingen åtgärd genom separata explicita
funktioner. Navigatorns kärna inför inget nytt granskningsläge och kräver
inget granskningskvitto. Revidering skapar ett nytt `AI_DRAFT` och nästa
analys rekommenderar därför REVIEW igen. Ett lyckat godkännande tar bort
REVIEW-kandidaten men gör inte utkastet till normal projektinformation.

Fritext är input till en redan vald funktion, aldrig routing:

- Skapa får generatorinstruktion;
- Revidera får revisionsinstruktion;
- Utred får en frågeställning för den explicit valda analysen;
- Visa och Exportera får ingen generell fritext.

Navigatorn samlar inte in eller vidarebefordrar någon egen generell fritext
eller godkännandekommentar. Efter REVIEW väljer projektledaren den separata
befintliga Task 1-funktionen Godkänn. Task 1:s valfria
godkännandekommentar förblir tillgänglig och ändras inte av
Navigator-kontraktet. Kommentaren är input till den redan explicit valda
Godkänn-funktionen och får aldrig påverka routing, kandidatval, action eller
rangordning.

#### Prioritetsnivåer

Den deterministiska policyn använder fyra hårda nivåer:

1. befintligt arbete eller identitetsproblem: REVIEW för `AI_DRAFT` och
   INVESTIGATE för ogiltig draft eller tvetydig/ogiltig normal artefakt;
2. CREATE med legitimt underlag när mjuka föregångare redan finns som
   `UNIQUE` normala projektartefakter, för Kommunikationsplan när minst en av
   dess två starka mjuka föregångare är `UNIQUE`, samt redo Mål, Riskanalys
   och Intressentanalys;
3. tillåten CREATE i annan ordning när kandidatens egna legitima källor räcker
   men en mjuk föregångare saknas;
4. INVESTIGATE när readiness eller underlag inte kan fastställas.

Global projektrots-, identitets- eller fasmismatch avbryter analysen före
rangordning. När inga kandidater återstår returneras NO_ACTION. Högst tre
rekommendationer visas.

Ett transient överhoppat råd gäller endast den aktuella presentationen. Det
får inte ändra kärnstate eller starta en dold funktion. Presentationen får
fylla på med nästa tillåtna kandidat från samma snapshot så att högst tre
icke-överhoppade rekommendationer fortsatt kan visas. Överhoppning rensas vid
projektbyte, ny explicit analys eller relevant lifecycleförändring och
persisteras aldrig som projekt- eller draftstate.

#### Export och återupptagning

Export är en separat åtgärd efter approval och ingår inte i Navigatorns steg,
prioritetsnivåer eller rekommendationsordning. En exporterad fil blir inte
projektkunskap. Navigatorn får endast visa processen att exportera, granska,
placera en accepterad kopia i `Projektplanering` och därefter ladda om
projektkontexten.

Navigatorns läge härleds på nytt från den aktiva projektroten, det aktuella
dokumentindexet och det projektbundna DraftStore. Artefakter som har skapats
utanför Navigatorn eller i en annan ordning respekteras. Navigatorn gör inga
generella freshnesspåståenden eftersom dagens källmetadata saknar stabila
innehållsfingeravtryck och källversioner. Den kan däremot deterministiskt
upptäcka att en normal Kommunikationsplans verifierbara STK-/Tidplansrelationer
eller Huvudaktivitetsordning inte längre kan groundas efter kontextomladdning;
detta ger `INVALID` och rådet `INVESTIGATE`. En efterföljande Revidera/REFINE
väljs fortfarande explicit av projektledaren och ingen artefakt muteras
automatiskt.

### Mål

Mål omfattar Effektmål och Projektmål. Den gemensamma domänmodellen ska minst
kunna representera `id`, `type`, `title`, `description` och
`success_criteria`. Käll-, relations- och kvalitetsfält kan komplettera
modellen, men domänkrav ska hållas åtskilda från Word-presentationen.

Mål-artefakten kan dessutom ha en valfri `project_commitment` på
artefaktnivå. Den beskriver projektidé eller åtagande samt resultat-, tids-
och kostnadsram när sådant underlag finns. Den är kontext för tolkning och
kvalitetsgranskning, inte ett Goal och inte ett krav för varje Mål-artefakt.

#### Effektmål

Gällande Projektdirektiv är normalkällan för Effektmål tidigt under planeringsfasen. När en fastställd Projektplan finns ska dess aktuella målbild väga tyngre för den gällande planen, medan Projektdirektivet fortsatt ger styrande bakgrund för mandat och ursprungligt uppdrag. Senare formella beslut kan komplettera eller ersätta uppgifter enligt informationsauktoriteten.

Mål-artefakten ska i normalläget dokumentera, strukturera och tolka de
gällande Effektmålen. Projektassistenten får inte tyst formulera om, ersätta
eller utöka dem. Ett oklart, motsägelsefullt eller icke verifierbart Effektmål
ska redovisas som en informationslucka eller en öppen fråga.

Projektassistenten får föreslå ett förtydligat eller förändrat Effektmål endast
när ett konkret behov har identifierats. Förslaget ska vara tydligt märkt som
ett förslag, ange vilket gällande Effektmål som berörs, förklara varför
förändringen kan behövas, redovisa underlaget och kräva mänskligt beslut.

Saknas Projektdirektiv ska Projektassistenten normalt uppmärksamma detta. Den
får inte presentera AI-genererade Effektmål som gällande.

#### Projektmål

Projektmål tas fram och förfinas med Effektmålen som styrande utgångspunkt och
med stöd av styrande dokument, hela `Projektplanering/**` inklusive
`Projektplanering/AI-Input` samt andra relevanta, tillgängliga
planeringskällor. De ska beskriva resultatet projektet ansvarar
för att uppnå och bör kunna relateras till de Effektmål de bidrar till.

Ett Projektmål har `contributes_to_effect_goal_ids` när relationen är känd och
kan beläggas; referenserna måste peka på befintliga Effektmål. `target_date`
är valfri i ett tidigt utkast. Effektmål har i stället spårbara styrande
`source_references`; en källa som framställs som gällande måste vara ett
gällande styrdokument eller ett senare formellt beslut enligt
informationsauktoriteten.

Projektmål ska successivt utvecklas mot SMART-kvalitet: specifika, mätbara,
accepterade, realistiska och tidsatta. Ett första arbetsutkast behöver inte
uppfylla alla dimensioner. Vid CREATE får agenten formulera preliminära
Projektmål, synliggöra saknade SMART-egenskaper, skapa öppna frågor och föreslå
hur målen kan bli tydligare och verifierbara. Vid REFINE ska den bevara sådant
som redan är tydligt, bedöma brister och redovisa saknad information utan att
fylla luckor med obekräftade antaganden.

Mot slutet av planeringsfasen är målbilden att varje Projektmål är tillräckligt
specifikt, har mätbara framgångs- eller acceptanskriterier, har mänskligt stöd
för att vara accepterat, har bedömts realistiskt utifrån tillgängliga
förutsättningar och har en tidpunkt eller tydlig tidsmässig avgränsning.
Agenten får inte själv fastställa att ett mål är Accepterat eller Realistiskt
utan stöd i projektinformationen. Ett `ArtifactDraft` som är `approved` är
bara godkänt för export; det betyder inte att Projektmålet är accepterat enligt
SMART eller PPS.

SMART är en härledd kvalitetsanalys i Sprint 4. Den ska utgå från målens
innehåll, framgångskriterier, eventuell tidsättning, källstöd, underlag för
acceptans och realism samt öppna frågor. Fem AI-genererade SMART-booleans eller
separata persistenta SMART-granskningsanteckningar ska inte införas utan ett
konkret senare behov.

Godkännande för export får ske med kvarvarande öppna frågor om ofullständig
planeringsinformation. De ska synliggöras som varningar. Däremot blockerar
ogiltig kanonisk struktur, dubbla mål-id:n, ogiltig måltyp, ogiltiga eller
hängande relationer, ett Projektmål som refererar till ett obefintligt
Effektmål, ett påstått gällande Effektmål utan giltig styrande källa och andra
domäninvarianter som gör artefakten internt ogiltig.

#### Målområden

Målområden är genererings- och analysstöd, inte en obligatorisk checklista.
Agenten identifierar relevanta områden, undviker att skapa mål enbart för att
fylla varje område och kan föreslå ett annat område när underlaget motiverar
det. Endast valda eller relevanta områden lagras i måldokumentet.

Katalogen omfattar Verksamhetsnytta; Användarnytta och användbarhet; Kvalitet
och effektivitet; Digital förmåga och arbetssätt; Informations- och
datakvalitet; Säkerhet och regelefterlevnad; Teknisk kvalitet och arkitektur;
Införande och användning; Förvaltning och långsiktighet; Ekonomi och
resursnytta; Tillgänglighet och inkludering; Hållbarhet; samt Genomförbarhet
och resurskapacitet. Det sista området avser att projektet ska kunna
genomföras utan ohållbar belastning eller oacceptabel påverkan på ordinarie
verksamhet, exempelvis inom UIT, IT-support, universitetsförvaltningen och
institutionerna.

#### Skalbar presentation

Måldokumentet måste fungera med många Effektmål och ännu fler Projektmål.
Presentationens princip är en kort översikt över alla mål, detaljerade
målbeskrivningar grupperade per Effektmål, tydlig spårbarhet från Projektmål
till Effektmål, kompakta målområdesetiketter samt SMART-bedömning och öppna
kvalitetsbrister i detaljvyn. Varje Projektmål har ett kanoniskt detaljblock
och presentationen får inte persistera egna kopior av översikt, grupper,
relationsmatris eller SMART-data. En kompakt many-to-many-relationsmatris hör
hemma i en bilaga. Word-renderingen ska kunna använda sidbrytningar och
upprepade tabellrubriker utan svårlästa målblock. Exakt layout ska valideras
med ett större syntetiskt exempel innan produktionsmallen låses.

Planeringsfasen ska på sikt stödja bland annat:

- Mål
- Leveranser
- Riskanalys
- WBS
- Tidplan
- Intressentanalys
- Kommunikationsplan
- Projektplan

Intressentanalys och Kommunikationsplan är separata artefakter men har en
naturlig relation. Sprint 7:s beslutade kontrakt gör Intressentanalys till en
strukturerad artefakt med stabila `STK-...`-ID:n och Kommunikationsplan till en
strategisk strukturerad artefakt med stabila målgrupps-ID:n, exempelvis
`KMG-...`. En målgrupp får referera 0..N verifierade Intressenter när en unik
normal Intressentanalys finns. Relationen är vägledande och gör inte
Intressentanalys till en obligatorisk hård föregångare.

Kommunikationsplanens kärna är en målgruppsstrategi och en matris över
målgrupp × projektskede med `Veta/Förstå`, `Känna/Inställning`, `Göra/Agera`
och `Kärnbudskap`. Generella kanaler och särskilda hänsyn hör till
målgruppsstrategin. Enskilda operativa kommunikationsaktiviteter med datum och
ansvar hör normalt hemma i WBS/Tidplan eller projektets operativa arbetsmodell,
inte i en konkurrerande aktivitetsplan i Kommunikationsplanen.

Kommunikationsplanen använder Tidplanens beslutade Huvudaktiviteter som normal
övergripande tidsaxel när en unik strukturerad Tidplan finns. Alla
Huvudaktiviteter ska normalt återfinnas i samma ordning i matrisen, även när
ingen särskild kommunikationsinsats planeras. Presentationen visar alltid den
mänskligt läsbara rubriken tillsammans med den stabila relationen; ett ID-only-
resultat är inte acceptabelt. Utvalda kommunikativt viktiga aktiviteter eller
milstolpar får komplettera tidsaxeln. Intressentanalys och Tidplan är starka
mjuka föregångare, aldrig hårda krav, och ändringar i dem får inte automatiskt
mutera Kommunikationsplanen.

Sprint 7:s primära Office-presentation för Intressentanalys och
Kommunikationsplan är XLSX. Kommunikationsplanens arbetsbok ska minst ha
`Övergripande strategi`, `Målgruppsstrategi` och `Kommunikationsmatris`. En
framtida Word-presentation kan utvärderas som ytterligare rendering av samma
kanoniska artefakt men är inte en separat sanningskälla.

Projektplanen är planeringsstadiets sammanhållande slutartefakt och en
accepterad Projektplan är ett styrdokument med normal hemvist i
`Styrdokument/`. Den sammanfattar och refererar Mål, Leveranser, WBS, Tidplan,
Riskanalys, Intressentanalys och Kommunikationsplan utan att skapa
konkurrerande kanoniska kopior eller ta över deras domänägarskap. Ingen av de
sju artefakterna är ett hårt CREATE- eller approval-krav; bristande mognad
synliggörs normalt som warning eller recommendation.

I Planering blockerar eller muterar en befintlig Projektplan inte REFINE av en
specialartefakt. Konflikter synliggörs. När en uppgift skulle ändra styrande
innehåll redovisas baseline och förslag och projektledaren beslutar uttryckligt.
I Genomförande ska en framtida konservativ UPDATE hantera ändringar från den
styrande baseline; fri REFINE gäller inte då. `phase + operation`, inte enbart
dokumentets existens, avgör beteendet.

### Leveranser

En Leverans beskriver ett projektresultat som ska lämnas över. `recipients`
(`Mottagare`) är den organisatoriska funktion eller part som förväntas ta emot
och ta ansvar för resultatet efter överlämning. `target_groups` (`Målgrupp`)
är de användare, grupper, verksamhetsdelar eller aktiviteter som förväntas
använda eller få nytta av resultatet. Begreppen är separata och får inte
kopieras mellan fälten. Båda är ordnade samlingar som kan vara tomma i ett
iterativt utkast; okända värden ska redovisas som öppna frågor och aldrig
uppfinnas av assistenten. En namngiven funktion som utifrån valda
projektkällor äger, förvaltar, driver eller har långsiktigt ansvar för
resultatet efter projektet är tillräckligt stöd för `recipients`, även utan
ordet "mottagare". Om ytterligare ansvar är osäkert ska det kända ansvaret
bevaras och endast den återstående osäkerheten bli en öppen fråga.

### WBS

WBS beskriver projektets hierarkiska resultat- och arbetsstruktur: vad
projektets arbete består av. Resultatperspektivet är normal riktning. WBS är
ett underlag för Tidplan, men är inte en Tidplan; WBS och Tidplan är separata
domäner.

Projektet är implicit root. WBS lagras som en flat, ordnad samling av
`WBSNode` med explicit `parent_id`; top-level-noder har `parent_id: null`.
Den kanoniska ordningen är depth-first pre-order: en parent kommer före sina
children, varje subtree hålls samman och sibling order följer samlingens
ordning. Djup och visningsnummer som `1`, `1.2` och `1.2.3` härleds från
hierarki och syskonordning och persisteras inte. Domänen har inget fast
maxdjup; Excel-begränsningar får inte bestämma domänens djup.

Varje WBSNode har ett stabilt, neutralt ID som `WBS-001`. `WBSNode.id` är
case-sensitive och ska matcha regex `^WBS-[0-9]{3,}$`. ID:t är unikt inom den
aktuella WBS:en, kodar inte position, hierarki eller nodtyp och ska bevaras
för samma semantiska nod vid revision. Detta är endast ett identitetsformat
inom artefakten; ingen global eller historisk ID-registry införs. Det finns två nodtyper:
`Arbetsområde` och `Arbetspaket`. Arbetsområden kan innehålla arbetsområden
eller arbetspaket. Arbetspaket är alltid löv. Deliverable är en separat domän;
någon `Leveransobjekt`-nodtyp införs inte.

Det presentationsoberoende WBSNode-kontraktet omfattar `id`, `parent_id`,
`type`, `title`, `description`, `contributes_to_deliverable_ids`,
`source_references`, `responsible` och `estimated_effort_hours`.
`contributes_to_deliverable_ids` är en ordnad relation med kardinalitet 0..N
på både arbetsområden och arbetspaket. Den anges där relationen faktiskt är
känd och relevant och kopieras inte mekaniskt genom hierarkin. Leveransdata
dupliceras inte i WBS; presentation och analys får härleda samlade relationer
för ett subtree utan att skapa ny persistent sanning.

`responsible` finns endast på arbetspaket och är `str | None`. Det kan vara en
person eller en organisatorisk grupp/funktion; `null` betyder okänt eller inte
fastställt och blank text avvisas. I ett AI-utkast får assistenten föreslå
preliminärt ansvar. När en verifierad namngiven person saknas ska förslaget
normalt vara en roll, funktion eller grupp. En namngiven person får endast
användas när personen uttryckligen finns i en vald projektkälla, valt AI-Input
eller projektledarens aktuella instruktion. En separat organisationsmodell
eller RAM/RACI ingår inte. Saknat ansvar blockerar inte automatiskt approval.

`estimated_effort_hours` finns endast på arbetspaket och är `int | None`.
Ett angivet värde måste vara ett positivt heltal; bool, float, sträng och noll
avvisas. Ett AI-utkast får föreslå ett preliminärt heltalsestimat när det ger
planeringsvärde; `null` används när ett sådant förslag skulle bli vilseledande.
Materiella antaganden bakom föreslaget ansvar eller estimat synliggörs normalt
som icke-blockerande öppna frågor, men mindre förslag kräver inte en fråga per
fält. Fältet är ett arbetsestimat, inte kalenderduration. Summeringar uppåt
härleds och persisteras inte; kostnad, timpris och resursmix ingår inte.
Presentation får inte visa en partiell summa som ett komplett totalestimat utan
att samtidigt indikera saknade estimat.

Första WBS-kontraktet innehåller inga persistenta aktiviteter och ingen
`WBSActivity`. WBS slutar vid arbetspaket; `ScheduledActivity` förblir
Tidplanens domänobjekt. Den valfria relationen
`ScheduledActivity.wbs_work_package_id: str | None` är implementerad: ett
arbetspaket kan ha 0..N aktiviteter och en aktivitet kan relatera till högst
ett arbetspaket. Domänobjektet och Tidplan-codecen validerar endast ID-syntax,
medan Tidplanens source boundary transient verifierar att varje använd relation
finns och avser ett `Arbetspaket` i en unik schemaidentifierad normal WBS-XLSX.
`STYRANDE/Gällande` prioriteras framför
`PROJECT_ARTIFACT/Projektunderlag`; flera källor på samma högsta nivå
legitimerar inga relationer och synliggörs som en öppen fråga. CREATE och
REFINE fungerar fortsatt utan verifierbar WBS, men då måste relationerna vara
`null`. Restore, REFINE, approval och export återvaliderar non-null-relationer
mot den normala projektkällan utan ett persistent ID-index. WBS lagrar inte
aktivitets-ID:n. AI-Input, AI-utkast och Export legitimerar inte relationer,
och ett arbetsestimat får aldrig automatiskt bli kalenderduration.

WBS använder samma princip för strikt Markdown som andra strukturerade
artefakter: `# WBS` följt av exakt ett synligt YAML-block med `nodes` och
`open_questions`. Markdown är den enda persistenta representationen och
typade domänobjekt rekonstrueras transient. `source_references` använder
`document` och `location` med regler mot blanka och dubbla värden. Strukturens
validering omfattar bland annat schema, nodtyper, unika ID:n, giltiga parents,
cykler, nåbar hierarki, canonical preorder, leaf-regeln, type-specific fields
och dubbletter. Den bevisar inte matematiskt PPS 100 %-coverage.

Top-level YAML-nycklar skrivs alltid deterministiskt i ordningen `nodes`, sedan
`open_questions`. Varje WBSNode skriver följande gemensamma fält i ordningen:
`id`, `parent_id`, `type`, `title`, `description`,
`contributes_to_deliverable_ids`, `source_references`. Båda samlingarna skrivs
alltid, även när de är tomma (`[]`). Varje `SourceReference` skriver först
`document` och sedan `location`; `location` får vara `null`. Ett Arbetspaket
skriver därefter alltid `responsible` och `estimated_effort_hours`, även när
värdena är `null`. Ett Arbetsområde får inte innehålla dessa två fält.

Varje `WBSOpenQuestion` skriver alltid `id`, `question`, `related_node_id` och
`blocking` i den ordningen. `related_node_id` får vara `null`. ID:t är
case-sensitive och ska matcha regex `^WBS-Q-[0-9]{3,}$`. Dessa ID-format är
endast identitetsformat inom artefakten; ingen global eller historisk registry
införs.

100 %-regeln hanteras på tre nivåer: hård strukturell validering, AI-baserad
kvalitetsanalys av exempelvis scope gaps och osäker nedbrytning, samt mänsklig
review av faktisk täckning. Artefaktnivåns `WBSOpenQuestion` innehåller `id`,
`question`, `related_node_id` och `blocking`. Normalt approval kräver kanonisk
giltig WBS, minst en top-level-nod och ett arbetspaket, barn till varje
arbetsområde och inga frågor med `blocking: true`. När blockerande frågor
kvarstår visas samtliga öppna frågor och rekommendationen är att de först reds
ut. Projektledaren kan därefter göra ett separat, uttryckligt val `Godkänn och
exportera ändå som utkast`. En vanlig bekräftelse eller fri text aktiverar inte
undantaget. Valet är WBS-specifikt, bevaras med det godkända utkastet för
exportens återvalidering och ändrar inte frågornas `blocking`-klassning,
canonical Markdown, versionshantering eller filnamn.

Undantaget gäller endast approvalpolicyn för öppna frågor. Schema, codec,
hierarki, projektidentitet och projektrot, källreferenser, Deliverable-grounding
och transient titelgrounding valideras fortsatt före status- eller
filmutation. Icke-blockerande frågor, saknat ansvar, saknat estimat och avsaknad
av Deliverable-relation är inte automatiska hinder. Approval betyder endast
godkänd export enligt artefaktlivscykeln, inte ett bevis på full scope coverage.

WBS → Deliverable verifieras nu mot exakta stabila ID:n i ett unikt normalt
Leveransdokument. `STYRANDE/Gällande` prioriteras framför
`PROJECT_ARTIFACT/Projektunderlag`; likvärdiga kandidater legitimerar ingen
relation. Den valda DOCX-filen öppnas transient och read-only under den explicit
aktiva projektroten. Endast den genererade Leveransöversiktens ID-/titelrader,
korsverifierade mot motsvarande detaljrubriker, bildar den tillåtna ID-mängden.
Fri brödtext, AI-Input, AI-utkast och Export kan aldrig legitimera ett ID.
Utan verifierbar grounding måste relationerna vara tomma.

WBS-domänen och canonical Markdown lagrar fortsatt endast den ordnade
`contributes_to_deliverable_ids`-relationen. Vid varje Excel-export förs samma
verifierade DOCX-groundings immutable ID-/titelpar transient genom
`GeneratedDocument.structured_content`; de skrivs aldrig till WBS.md,
DraftStore, source metadata eller en separat fil. Den befintliga kolumnen
`Leveranser` visar varje relation som `L-001 – Titel`, kommaseparerad i nodens
befintliga ID-ordning. ID:t visas alltid. Om en använd relation saknar exakt
verifierad titel avvisas exporten utan ID-only fallback eller placeholder.
En WBS utan Deliverable-relationer kan fortsatt exporteras utan
Leveransdokument.

CREATE och REFINE får den exakta tillåtna ID-listan och varje modellresultat
kontrolleras innan Markdown eller DraftStore muteras. Restore, approval och
export öppnar källan på nytt så att borttagna eller ändrade ID:n blir orphaned
och avvisas. Domänmodellen och codecen fortsätter endast att validera WBS-
formatet utan fil- eller projektlookup. Dold DraftStore-läsning, sidecars,
globalt ID-register och databas är inte tillåtna lösningar.

WBS CREATE och REFINE använder en deterministisk, begränsad projektion av de
valda källornas redan indexerade text. Små källor tas med i sin helhet; större
källor delas i cirka 2 000 tecken långa delar där början och relevanta avsnitt
om scope, leveranser, ansvar, roller, resurser, estimat och arbetsnedbrytning
prioriteras. Högst 8 000 tecken per källa och 32 000 tecken totalt skickas till
modellen. REFINE laddar atomiskt om projektets dokumentindex och väljer WBS-
källor på nytt, så nytillkommet AI-Input kan användas i samma session. Endast
source metadata för den faktiskt använda aktuella källmängden persisteras;
källinnehållet dupliceras inte i utkastets front matter.

Gemensam dokumentinläsning stöder även `.txt` som strikt UTF-8 eller UTF-8 med
BOM upp till 1 MiB. Binärt innehåll, NUL-byte, ogiltig UTF-8 och större filer
avvisas och isoleras som andra dokumentläsfel. Runtime-mappar förblir
exkluderade.

### Tidplan

Tidplan representerar projektets aktiviteter. En Office-arbetsbok är en
presentation av denna information, inte dess domänmodell.

Det kanoniska domänobjektet är `ScheduledActivity`. Sprint 3 kräver:

- `id`
- `title`
- `type` (`Aktivitet` eller `Milstolpe`)
- `status` (ett kontrollerat statusvärde)
- `start_date`
- `end_date`

Följande fält är valfria i den första modellen:

- `description`
- `owner`
- `progress` (0–100, standard 0)
- `depends_on` (stabila aktivitets-id:n)

Sprint 7:s beslutade utökning tillför en enkel övergripande Huvudaktivitet,
tekniskt `ScheduleGroup`, med stabilt `TPG-...`-ID, titel, valfri beskrivning
och ordning. En `ScheduledActivity` kan tillhöra högst en Huvudaktivitet.
Huvudaktivitetens tidsintervall härleds normalt från dess aktiviteter och
persisteras inte som en konkurrerande datumkälla. Huvudaktiviteter är en
orienterings- och tidsaxel, inte en WBS-hierarki; WBS får informera grupperingen
men ingen hård WBS→Huvudaktivitet-relation införs.

Aktivitetsfält för projektfas och leveransrelation skjuts upp tills deras
taxonomi respektive domänidentitet har definierats. De kan införas senare utan
att den arkitektoniska inriktningen förändras.

Slutdatum får inte föregå startdatum. En milstolpe har samma start- och
slutdatum. Beroenden får inte vara duplicerade eller referera till aktiviteten
själv; kontroll av okända beroenden och cykler sker senare för hela tidplanen.

Domänmodellen är oberoende av Excels layout.

## CREATE

Skapar en första version av en artefakt.

Under Planering får agenten använda:

- alla läsbara aktuella styrdokument enligt informationsauktoriteten
- alla läsbara dokument i `Projektplanering/**`, inklusive AI-Input
- andra relevanta planeringsartefakter
- metodik och mallar
- generell projektkunskap som tydligt preliminärt förslag där detta är lämpligt

Resultatet är ett arbetsutkast.

## REFINE

Förädlar en artefakt som fortfarande befinner sig i planeringsfasen.

REFINE får exempelvis:

- komplettera
- omstrukturera
- förbättra formuleringar
- lägga till nya analyser
- förändra upplägg

så länge förändringen är förenlig med styrande information.

---

# Genomförande

När projektet går in i genomförandet förändras agentens roll.

De styrande dokumenten är nu etablerade och utgör projektets aktuella plan.

Projektassistenten har två huvudsakliga uppgifter:

1. rapportera status
2. hålla styrdokumenten aktuella

Den detaljerade käll- och UPDATE-modellen för Genomförande är fortfarande framtida discovery. Följande riktning är sparad som underlag och ska inte behandlas som implementerat beteende förrän den senare sprinten har fastställt kontraktet.

---

## Statusrapportering

Statusrapportering innebär att jämföra:

```text
Plan
mot
Verklighet
```

Agenten utgår från projektets aktuella styrdokument och jämför dem med ny information om projektets genomförande.

Statusrapporten ska exempelvis beskriva:

- status i förhållande till plan
- genomförda aktiviteter
- avvikelser
- risker
- budget
- resurser
- tidplan
- behov av beslut
- rekommenderade åtgärder

Status kan exempelvis uttryckas:

- Grön – enligt plan
- Gul – avvikelse som projektet bedöms kunna hantera
- Röd – avvikelse som behöver hanteras av styrgrupp eller annan styrande nivå

### Statusrapport till styrgruppen

Relativt detaljerad.

Ska ge styrgruppen tillräckligt underlag för styrning och beslut.

### Statusrapport till portföljstyrningen

Mycket kortfattad.

Fokus på övergripande status, särskilt:

- tid
- budget
- resurser
- leveranser
- verksamhetspåverkan

Vid röd status ges mer information om orsaker och behov av åtgärder.

## Preliminär framtida källprincip

För framtida statusrapportering är `Styrdokument/**` den aktuella fastställda baslinjen. Om ett senare formellt styrgruppsbeslut tydligt motsäger ett styrdokument ska Projektassistenten normalt varna för att styrdokumentet kan vara inaktuellt och rekommendera UPDATE innan statusrapporten färdigställs, i stället för att tyst skapa en alternativ planinterpretation.

`Styrgrupp/**` är en kronologisk händelsehistorik och ska inte automatiskt läsas i sin helhet vid varje operation. Relevanta styrgruppsmöten ska i den framtida modellen väljas explicit eller genom ett deterministiskt föreslaget urval som projektledaren bekräftar.

---

# UPDATE under genomförandet

UPDATE skiljer sig fundamentalt från REFINE.

REFINE innebär:

> Vi bygger fortfarande dokumentet.

UPDATE innebär:

> Dokumentet är etablerat och ska hållas korrekt utifrån vad som faktiskt har hänt i projektet.

UPDATE syftar därför inte till att skriva om dokumentet.

UPDATE syftar till att bevara projektets styrning.

Agenten ska:

- utgå från den aktuella styrande versionen
- göra minsta nödvändiga förändring
- kunna motivera varje förändring
- ange vilken ny information som motiverar ändringen
- bevara sådant som fortfarande gäller
- identifiera konflikter mellan nya uppgifter och gällande styrning
- markera när ett nytt beslut krävs

Agenten ska inte:

- omstrukturera ett styrdokument utan tydligt behov
- skriva om fungerande delar av dokumentet
- behandla arbetsmaterial som ett nytt beslut
- ändra mål eller andra beslutade förutsättningar utan stöd i beslut

Den framtida målbilden är att `Styrdokument/**` utgör baseline för vad planen är, medan senare relevanta formella beslut kan fungera som förändringsauktoritet för vad som ska ändras under en explicit UPDATE. Efter mänsklig granskning och fastställande ersätter den nya versionen den tidigare i `Styrdokument`.

För varje väsentlig förändring bör agenten kunna redovisa:

- vad som ändras
- vad som stod tidigare
- vad som föreslås nu
- varför ändringen behövs
- vilka källor som stödjer den

Exakt execution-phase source selection, provenance/baseline, UPDATE-lifecycle och statusrapportering beslutas i en senare sprint.

---

# Styrgrupp

`Styrgrupp` organiseras efter styrgruppsmöten.

Varje möte är en sammanhållen projekthändelse och representeras därför av en mapp.

Exempel:

```text
Styrgrupp/
    2026-09-15 SG01/
        Statusrapport.docx
        Presentation.pptx
        Minnesanteckningar.docx
        Beslut.docx

    2026-10-20 SG02/
        Statusrapport.docx
        Presentation.pptx
        Minnesanteckningar.docx
        Beslut.docx
```

Alla dokument som hör till ett möte ligger tillsammans.

Det gör det möjligt för både projektledaren och agenten att förstå:

- vilket underlag som hörde till mötet
- vad som presenterades
- vilka frågor som diskuterades
- vilka beslut som fattades
- hur projektets styrning utvecklats över tid

Mappen får innehålla andra dokument som hör till mötet utan att nya dokumenttypsmappar behöver skapas.

För framtida Genomförande/UPDATE behandlas Styrgrupp som historisk händelsekälla, inte som en aktuell sanningsyta där allt alltid läses. Den planerade webbinteraktionen är att visa identifierade styrgruppsmöten som checkboxar och deterministiskt förvälja möten som sannolikt är aktuella; projektledaren kan acceptera, lägga till eller ta bort möten före operationen.

Inom ett valt möte är den preliminärt beslutade informationsvikten:

```text
Beslut
↓
Minnesanteckningar
↓
Statusrapport ≈ Presentation
```

Nyare material slår inte automatiskt äldre material med högre auktoritet. Ett senare explicit beslut kan däremot ersätta ett tidigare beslut. Denna modell implementeras inte i Sprint 7 utan sparas för senare execution-discovery.

---

# Projektavslut

## Syfte

Sammanfatta projektets resultat och erfarenheter.

Agenten ska kunna använda:

```text
Styrdokument/
```

```text
Styrgrupp/
```

```text
Projektavslut/AI-Input/
```

samt relevanta statusrapporter och andra verifierade projektresultat.

Resultatet är framför allt:

- Slutrapport
- Erfarenhetsåterföring
- Överlämningsunderlag

---

# Artefaktoperationer

Projektassistentens produktmodell använder operationer för att beskriva vad som ska göras med en artefakt.

## CREATE

Skapa en ny artefakt.

## REFINE

Iterativt utveckla en artefakt under planeringsfasen.

## UPDATE

Kontrollerat uppdatera ett etablerat styrdokument.

## ANALYZE

Analysera projektinformation utan att skapa eller förändra ett styrande dokument.

## CREATE_REPORT

Skapa en rapport baserad på projektets styrning och aktuella läge.

## CREATE_FINAL_REPORT

Skapa slutrapport eller motsvarande avslutande artefakt.

Alla operationer behöver inte vara tillgängliga för alla artefakter eller i alla projektfaser.

Tillgängligt beteende bestäms av:

```text
Artefakt + Operation + Fas
```

---

# AI-utkast

AI-genererade arbetsutkast ska hållas separerade från projektets officiella dokument.

```text
AI-utkast/
```

används för agentens persistenta arbetsutkast.

Ett projekt kan ha flera samtidiga arbetsutkast, med ett utkast per
artefakttyp, exempelvis:

```text
AI-utkast/
    Riskanalys.md
    WBS.md
    Tidplan.md
```

Artefakttypen identifierar utkastet. Projektledaren kan därför exempelvis be
att få visa ett riskanalysutkast, revidera en tidplan eller godkänna en WBS.
Om flera utkast finns och projektledaren inte anger vilket som avses ska
assistenten be om ett förtydligande i stället för att gissa.

Ett AI-utkast är inte ett styrande projektdokument bara för att det har godkänts för export.

Projektledaren ansvarar för att verifiera resultatet och besluta när och hur det blir en del av projektets ordinarie dokumentation.

Under Planering kan projektledaren efter mänsklig granskning välja `Revidera
accepterad [artefakt]` från exakt en läsbar och unik normal artefakt, eller
`Skapa ny [artefakt] från grunden`, som utesluter tidigare draft och normala
versioner av samma typ. Båda vägarna återanvänder den befintliga livscykeln,
läser aktuella legitima källor och skapar ett nytt `AI_DRAFT`; ingen normal fil
ersätts automatiskt.

En uttrycklig funktionsspecifik instruktion får dessutom beställa preliminära
poster eller ändringar i ett AI-utkast utan motsvarande dokumentstöd. Den är
auktoritativ för utkastets önskade innehåll, men är inte en projektkälla och får
inte skapa falska källreferenser. Okända valfria relationer och värden lämnas
som tomma listor, `null` eller öppna frågor enligt artefaktens kontrakt;
codec-, projekt-, ID-, struktur- och persistence-invarianter gäller fortsatt.

---

# Export

Genererade dokument placeras i:

```text
Export/
```

innan projektledaren har verifierat och placerat dem på rätt plats i projektets ordinarie dokumentstruktur.

Det ger en tydlig gräns mellan:

- AI-genererad output
- verifierad projektinformation

---

# Produktdesignprinciper

## Transparent

Projektledaren ska kunna förstå vilket underlag agenten har använt.

## Kontrollerbar

Projektledaren styr aktuellt arbetsmaterial genom placering i rätt aktiva informationsdomän och kan använda `AI-Input` som en explicit extra ingång.

## Spårbar

Väsentliga förändringar ska kunna kopplas till sina källor.

## Fasmedveten

Projektfasen förändrar agentens beteende.

## Konservativ vid UPDATE

Etablerade dokument ändras så lite som möjligt.

## Kreativ vid CREATE och REFINE

Under planeringen får agenten bidra med analys och struktur inom ramen för styrande information.

## Mänskligt styrd

Människan fattar projektbeslut och godkänner dokument.

## Förutsägbar

Projektledaren ska kunna förstå varför en viss källa, operation och metod användes.

## Enkel administration

Produktens beteende ska så långt möjligt styras genom en begriplig projektstruktur snarare än komplex metadata.

Metadata används där maskinläsbar information verkligen behövs, men ska inte ersätta en mappstruktur som är tydlig för projektledaren.
