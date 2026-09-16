# Arkitektur

## Nuläge

Projektet är i dokumentationsbootstrap/Sprint 0. Ingen applikation är implementerad ännu. Det finns därför inga etablerade klient-, API-, databas- eller driftkomponenter att beskriva som befintliga.

## Beslutad principiell målbild

Strukturerad applikationsdata ska vara beständig källa. Recept, råvaror, planer och inköpslistor ska kunna lagras, granskas, ändras och valideras som strukturerade data. AI ska främst tolka, föreslå och assistera. AI-genererad löptext får inte vara den enda representationen av domändata.

## Föreslagen inriktning

Mobilanpassad webb/PWA är dokumenterad som `Proposed`, eftersom mobil användning är central och en gemensam webbklient är en möjlig tidig väg. Krav på exempelvis offline, installation och notifieringar är inte fastställda.

## Tekniska kandidater

Följande har diskuterats men är inte valda eller implementerade: Next.js/React/TypeScript, Python/FastAPI, PostgreSQL/Supabase, Supabase Auth/Storage/Realtime, GitHub Actions och managed hosting.

Exakt stack, drift, AI-upplägg, fotoanalys, prisdata och butiksintegrationer förblir öppna tills de beslutats. Framtida lager och gränssnitt ska väljas utifrån MVP, domänmodell, säkerhet och deploymentkrav.

## Principiella gränser

AI-lager, produktdata och externa datakällor bör kunna verifieras och bytas separat. Ett förslag ska inte automatiskt bli ett produktbeslut eller en beständig sanning. Den konkreta lagerindelningen och integrationsarkitekturen tas fram först när tillräckligt produktunderlag finns.
