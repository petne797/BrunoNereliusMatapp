# Architecture Decision Records

Status: `Accepted` = fattat beslut, `Proposed` = föreslagen inriktning, `Pending` = öppen beslutsfråga.

## ADR-001 – Strukturerad applikationsdata som beständig källa

- Status: Accepted
- Beslut: Recept, råvaror, planer och inköpslistor ska ha strukturerade beständiga representationer. AI ska främst tolka, föreslå och assistera och får inte vara ensam källa.
- Motiv: Informationen ska kunna granskas, ändras, återanvändas och valideras.

## ADR-002 – Mobilanpassad webb/PWA

- Status: Proposed
- Inriktning: En mobilanpassad webbapplikation/PWA utreds framför separata native-appar.
- Motiv: Mobil användning är central och en gemensam klient kan vara lämplig i ett tidigt skede.
- Öppet: krav på offline, installation, notifieringar och slutlig plattformsstrategi.

## Öppna arkitekturfrågor

Exakt stack, drift, AI-upplägg, fotoanalys, prisdata, butiksintegrationer, autentisering, lagring och synk är inte beslutade. De ska dokumenteras i nya ADR:er först när ett konkret beslut finns.
