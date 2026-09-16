# Samarbetsmodell

Detta dokument beskriver den långlivade arbetsmodellen för Bruno Nerelius Matapp.

## Permanent sanningskälla

GitHub-repot `petne797/BrunoNereliusMatapp` är permanent sanningskälla för dokumenterade produktbeslut, arkitekturbeslut, utvecklingsregler och faktiskt repo-tillstånd. ChatGPT-sessioner är arbetskontext, inte permanent sanningskälla.

Om GitHub-dokumentation, faktiskt repo-tillstånd och tidigare konversationskontext motsäger varandra ska skillnaden redovisas. När GitHub Projects senare etableras kan det vara operativ sanningskälla för backlog, sprinttillhörighet och taskstatus. Repo-dokumentationen förblir sanningskälla för långlivade beslut och principer.

## Roller

- Petter är Product Owner och ansvarar för slutliga prioriteringar, produktbeslut och acceptans.
- Petter och Clara arbetar gemensamt med produktbehov, verksamhetsperspektiv, UX, användarflöden och användartestning.
- ChatGPT stöder produktarbete, analys, planering, arkitektur, sprintledning och review.
- Work används vid större sammanhängande dokumentations-, analys- eller repoarbeten när det ger tydlig nytta.
- Codex används för implementation och avgränsat repoarbete.

Codex får inte själv fatta saknade produktbeslut eller göra implicita prioriteringar.

## Två arbetslägen

### Explorativt produkt- och UX-arbete

`PROD` och `UX` får utforska behov, beteenden, problem, hypoteser och idéer. En idé är inte ett beslut, och GitHub behöver inte läsas inför varje explorativ fråga.

Aktuell `PRODUCT.md` och relevant dokumentation ska däremot kontrolleras innan beslutat produktläge sammanfattas, MVP avgränsas, roadmap prioriteras eller text för repo-dokumentationen formuleras.

### Arkitektur, sprint och implementation

Repo-bootstrap är obligatorisk före analys, planering eller rekommendation inom arkitektur-, sprint-, implementation- och repoarbete. Läs först `COLLABORATION.md`, därefter relevant dokumentation. Vid nulägesfrågor ska minst `SPRINTS.md` och relevant task- eller sprintunderlag kontrolleras.

## Sessioner och namnstandard

Normalt används följande långlivade chattar:

- `META` – arbetssätt, verktyg och process
- `PROD` – produktvision, behov och användningsfall
- `UX` – användarflöden, design och användartestning
- `PLAN` – roadmap, prioritering och långsiktig planering
- `ARCH` – arkitektur och teknik

Varje sprint får normalt en separat `SPRINT N`-chat. Varje avgränsad implementationstask får normalt en separat Codex-session. Större dokumentations- eller repoarbeten kan få separat Work- eller Codex-session.

## Codex-bootstrap

En ny Codex-session som ska ändra repot ska minst:

1. verifiera arbetskatalog och Git-root;
2. läsa `AGENTS.md`;
3. läsa `COLLABORATION.md`;
4. läsa relevant `SPRINTS.md`, taskunderlag och annan relevant dokumentation;
5. kontrollera `git status --short`;
6. stoppa och rapportera om repo, branch, status eller dokumentation inte stämmer med taskens antaganden.

Permanenta regler ska inte behöva dupliceras i varje Codex-prompt.

## Standardflöde för implementationstask

1. Behov och task identifieras.
2. Eventuell produkt- eller arkitekturfråga löses före implementation.
3. Scope och acceptanskriterier fastställs.
4. En separat Codex-session startas.
5. Task branch skapas från aktuell `master`.
6. Implementation och relevanta tester genomförs.
7. Commit, push och lättviktig PR skapas.
8. Chat granskar faktisk GitHub-diff och SHA.
9. Korrigeringar görs i samma Codex-session, branch och PR.
10. Petter gör eventuell produkt- och UX-QA, vid behov tillsammans med Clara.
11. Petter fattar slutligt acceptansbeslut.
12. Godkänd ändring squash-mergas till `master`.

## Review och QA

Gröna tester betyder inte automatiskt att tasken är accepterad. Review ska kontrollera scope, arkitektur, tester, dokumentation och orelaterade ändringar. Codex ansvarar i första hand för automatiserad teknisk verifiering. ChatGPT ansvarar för kod- och arkitekturreview via faktisk GitHub-diff. Användar- och produkt-QA görs av Petter och, vid relevanta produktfrågor, tillsammans med Clara.

## Dokumentansvar

- `PRODUCT.md` – dokumenterad produktdefinition.
- `ADR.md` – fattade arkitekturbeslut.
- `ARCHITECTURE.md` – aktuell och planerad systemarkitektur.
- `ROADMAP.md` – långsiktig riktning.
- `SPRINTS.md` – sprintmål och historik på den nivå som inte bättre hanteras av GitHub Projects.
- `AGENTS.md` – regler för arbete inne i repot.
- `IDEAS.md` – ännu ej beslutade idéer.
- `COLLABORATION.md` – samarbets- och utvecklingsmodell.

Dokumenten ska ha tydliga ansvarsgränser och inte duplicera varandra i onödan.
