---
name: content-ideation
description: Generera 4 veckors content för en B2B-marknadsförare fördelat på fyra content-typer — 3 short form video, 2 image posts, 1 long form video och 1 newsletter per vecka. Bygger på ICP-spec, VoC-rapport och brand guidelines som ärvs från Project. Pushar rakt in i Airtable Content OS (Idéer + Content + Distribution-tabeller) via MCP — annars renderar markdown-tabell. Använd den varje gång användaren ber om ideation, content-plan, veckans content, content kalender, eller hänvisar till Modul 4 lektion L4.1. Använd även om användaren bara skriver "ideation content" eller "veckans content" — denna skill ÄR Content OS:s ideation-motor.
---

# content-ideation

Skill för Modul 4 lektion L4.1 — content-ideation för B2B-marknadsförare. Genererar en månads pipeline (4 veckor) fördelat på fyra content-typer, baserat på kundens ICP och VoC. Pushar färdiga idéer in i Airtable Content OS.

## Vad denna skill ÄR till för

- Generera 4 veckors content-plan på en gång (en månads pipeline)
- Mappa varje idé mot ICP-smärta + VoC-citat så det inte blir generiskt
- Fördela mellan fyra content-typer enligt fast schema (3 SFV + 2 image + 1 LFV + 1 newsletter per vecka)
- Pusha färdiga idéer in i Airtable Content OS — Idéer-tabell först, sedan Content-tabell + Distribution när idén är godkänd

## Vad denna skill INTE gör

- Skriva färdiga manus eller copy för enskilda posts → använd `marketing-content-generator` eller `manus-writer`
- Producera short form video från idé till klar fil → använd `short-form-video`
- Sätta upp Airtable-basen från scratch → schema-spec finns i `references/airtable-schema.md`, basen måste byggas av användaren
- Genererar inga claims om virala hooks eller engagemang utan stöd i VoC

## Triggers

Skillen aktiveras när användaren skriver:

- "ideation content"
- "veckans content"
- "content-plan för [period]"
- "kör ideation för [klient/ICP]"
- "fyll på Content OS"
- "ge mig 4 veckors content"
- Hänvisning till Modul 4 / L4.1

Om användaren ber om något som inte är ideation — hänvisa till rätt skill istället.

## Inputs som behövs innan du börjar

1. **ICP-spec** — från `icp-spec.md` i Project. Måste innehålla minst: smärta-topplista, jobs-to-be-done, plattformar där ICP hänger.
2. **VoC-rapport** — från `voc-rapport.md` i Project. Måste innehålla minst: 10+ verkliga citat per smärta, kategoriserade efter funnel-stadie.
3. **Brand guidelines** — från `brand-guidelines.skill.md` i Project. Voice + förbjudna ord + visuell riktning.
4. **Vecknummer eller startdatum** — vilka 4 veckor planen ska täcka.
5. **Airtable Content OS-base-ID** — om MCP är ansluten. Annars genereras markdown-tabell.

Om något fattas — fråga användaren innan du börjar. Generera inte content på lös basis. Generisk content är värre än ingen content.

## Workflow

### Steg 1: Läs in input

Läs in `icp-spec.md`, `voc-rapport.md`, `brand-guidelines.skill.md` från Project. Notera:

- 3-5 topp-smärtor från ICP
- 10-20 VoC-citat som ska kunna mappas mot idéerna
- Förbjudna ord och voice-regler

### Steg 2: Distribuera content-typerna

Per vecka:

| Typ | Antal | Plattformar | Längd |
|---|---|---|---|
| Short form video | 3 | LinkedIn + Instagram Reels + YouTube Shorts | 15-30 sek |
| Image post | 2 | LinkedIn + Instagram feed | 90-150 ord body |
| Long form video | 1 | YouTube (5-10 min) + repurpose till LinkedIn-artikel | 5-10 min |
| Newsletter | 1 | ESP (HighLevel/MailChimp/HubSpot) | Topp-5 nyheter + en åsikt |

**Totalt per vecka: 7 content-pieces. Per månad: 28 pieces.**

Full anatomi per typ — Hook/Body/CTA-struktur, hooks-bibliotek, exempel — i `references/content-types.md`. Läs den innan första ideationen i en session.

### Steg 3: Idégenerering per content-typ

För varje slot (vecka 1 SFV #1, vecka 1 SFV #2, ...) genererar du:

- **Hook** (max 15 ord) — bygg på en VoC-citat eller en topp-smärta
- **Vinkel** (1 mening) — vad är kontroversiellt/oväntat/specifikt
- **Body-outline** (3-5 punkter) — vad sägs, vilken ordning
- **CTA** (1 mening) — vad ska tittaren göra
- **Distribution** (lista plattformar)
- **ICP-smärta mappad** (referens till specifik smärta-ID från `icp-spec.md`)
- **VoC-citat mappat** (en eller flera citat från `voc-rapport.md` som motiverar vinkeln)

### Steg 4: Dependency-check

Innan leverans — kontrollera att:

- Ingen vecka har två SFV med samma hook eller samma vinkel
- Inte mer än 1 newsletter och 1 LFV per vecka
- Minst 3 av 4 veckor har minst 1 piece per ICP-topp-smärta
- Long form video-temat per vecka kan rendera 3-5 derivata SFV-idéer (LFV är ankaret, SFV är derivat när möjligt)

Om något bryter mot dessa regler — omformulera, byt slots.

### Steg 5: Push till Airtable Content OS

Om Airtable-MCP är ansluten:

1. Skapa records i **Idéer-tabell** först (en record per piece)
2. Vänta på användarens godkännande (status: Godkänd / Pausa / Skrota)
3. När godkänd — skapa record i **Content-tabell** kopplat till Idéer-recorden
4. För varje plattform i Distribution-spec — skapa record i **Distribution-tabell** kopplat till Content-recorden

Full schema-spec — fält, relations, formler, view-design — i `references/airtable-schema.md`.

Om MCP inte är ansluten — leverera som markdown-tabell strukturerad så att copy-paste till Airtable funkar.

## Output-format

### Variant A: Markdown-tabell (om MCP saknas)

```markdown
# Content-plan: [Klient/ICP-namn] — [startdatum till slutdatum]

## Vecka 1 ([datum])

### SFV #1: [Hook]
- **Vinkel:** ...
- **Body:** 1. ... 2. ... 3. ...
- **CTA:** ...
- **Distribution:** LinkedIn, IG Reels, YT Shorts
- **ICP-smärta:** [smärta-ID]
- **VoC-citat:** "..." — [persona]

### SFV #2: ...
[fortsätt för alla 7 pieces]

## Vecka 2 ...
[fortsätt för alla 4 veckor]
```

### Variant B: Airtable Content OS (om MCP ansluten)

Bekräfta:

- N idéer skapade i Idéer-tabell (länk till view)
- Status sätts till "Förslag — väntar på godkännande"
- Användaren får lista över record-ID + hook för att kunna granska snabbt

## Failure conditions

Skillen ska INTE leverera content om:

- ICP-spec saknas eller är tom — content blir generisk
- VoC-rapport saknas — du har inga citat att mappa mot, vinklar blir påhittade
- Brand guidelines saknas — risk för förbjudna ord och voice-brott
- Användaren ber om mer än 4 veckor på en gång — kör 4 veckor först, godkänn, sedan nästa månad. Annars rasar kvaliteten.
- Användaren ber om generisk content "för B2B" utan ICP — fråga vilken ICP innan du börjar

## References

- `references/airtable-schema.md` — Content OS-schema för 3 tabeller (Idéer, Content, Distribution). Fält, fälttyper, relations, formler, rekommenderade views.
- `references/content-types.md` — Detaljerade specs per content-typ. Hook/Body/CTA-anatomi, hooks-bibliotek per smärta, längd-regler, plattforms-specifika anpassningar.

## Self-check (obligatorisk innan leverans)

- [ ] 4 veckor × 7 pieces = 28 pieces totalt
- [ ] Fördelning per vecka: 3 SFV + 2 image + 1 LFV + 1 newsletter
- [ ] Varje piece har Hook, Vinkel, Body, CTA, Distribution, ICP-smärta, VoC-citat
- [ ] Ingen vecka har dubbletter av hook eller vinkel
- [ ] Minst 3 av 4 veckor täcker alla ICP-topp-smärtor
- [ ] LFV per vecka kan rendera 2-3 derivata SFV
- [ ] Inga förbjudna ord (kör grep mot brand guidelines)
- [ ] Inga AI-tells i hooks (revolutionerande, transformera, unlock, spelförändrande, 10x)
- [ ] Airtable-records skapade ELLER markdown-tabell levererad
