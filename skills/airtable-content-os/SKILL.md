---
name: airtable-content-os
description: Setup-spec för Airtable-basen "Content OS" från Modul 4 — tre tabeller (Idéer, Content, Distribution) med fält, relations och veckogransknings-interface. Aktivera när användaren skriver "skapa Content OS i Airtable", "Airtable content-bas", "Content OS schema", "sätt upp content-pipeline i Airtable", eller frågar hur Airtable MCP kopplas till basen. Levererar fullt schema-spec som kan implementeras manuellt eller via Airtable MCP.
---

# airtable-content-os

Schema-spec för Content OS-basen i Airtable — Modul 4-leverabeln.

## Vad skillen gör

Beskriver exakt struktur för Content OS-basen: tabeller, fält per tabell, relations, och hur veckogranskning fungerar som interface. Skillen är referens — implementering sker antingen manuellt i Airtable eller via Airtable MCP.

## Triggers

Aktivera när användaren skriver:

- "Skapa Content OS i Airtable"
- "Airtable content-bas"
- "Content OS schema"
- "Sätt upp content-pipeline i Airtable"
- "Hur kopplar jag Airtable MCP till content-basen"
- "Vilka fält ska content-tabellen ha"

## Bas-struktur

Tre tabeller med relations:

```
Idéer ──(1:N)──> Content ──(1:N)──> Distribution
```

En idé blir ett eller flera content-stycken. Ett content-stycke distribueras till ett eller flera kanaler.

## Tabell 1: Idéer

Råa idéer innan dom blir innehåll. Lågfriktion att fånga upp.

| Fält | Datatyp | Notering |
|---|---|---|
| Titel | Single line text | Kort beskrivning av idén |
| Källa | Single select | VoC-citat, Eget tanke, Konkurrent, NotebookLM, Kund-intervju, Annat |
| Smärta | Long text | Vilken ICP-smärta adresseras |
| Vinkel | Long text | Hur vi vill rama in det |
| Status | Single select | Ny, Behandlas, Konverterad, Avslutad |
| ICP-segment | Multi select | Vilka segment idén riktar sig till |
| Skapad | Created time | Auto |
| Notes | Long text | Friskrivning |

## Tabell 2: Content

Det faktiska innehållet — drafted, schemalagt eller publicerat.

| Fält | Datatyp | Notering |
|---|---|---|
| Titel | Single line text | Arbetsnamn |
| Idé | Link to Idéer | 1:1 eller N:1 till Idéer |
| Format | Single select | LinkedIn-post, Cold email, Blog, Video-manus, Carousel, Annat |
| Draft | Long text | Den faktiska texten |
| Status | Single select | Idea, Drafting, Review, Approved, Scheduled, Published |
| Hook-rating | Number 1-5 | Manuell bedömning av hook-styrka |
| Owner | Single collaborator | Vem ansvarar |
| Brand-check | Checkbox | Har passerat brand-guidelines.skill.md |
| ICP-check | Checkbox | Har passerat icp-summary.md |
| Skapad | Created time | Auto |
| Senast ändrad | Last modified time | Auto |

## Tabell 3: Distribution

Varje publicering — samma content kan distribueras på flera ställen.

| Fält | Datatyp | Notering |
|---|---|---|
| Content | Link to Content | N:1 till Content |
| Kanal | Single select | LinkedIn, X, Newsletter, Blog, YouTube, Reddit, Annat |
| Publiceringsdatum | Date | När det går ut |
| Status | Single select | Scheduled, Published, Failed, Cancelled |
| URL | URL | Länk till publicerat material |
| Impressions | Number | Manuell eller via integration |
| Engagement | Number | Likes + kommentarer + delningar |
| Replies/DMs | Number | Direkta reaktioner |
| Konverteringar | Number | Leads/möten genererade |
| Notes | Long text | Vad funkade/inte |

## Veckogransknings-interface

Bygg en Airtable Interface med tre vyer:

### Vy 1: "Veckans content-review"
- Visar Content-records med Status = Review
- Sorterat på Skapad descending
- Filter: Brand-check och ICP-check måste vara markerade innan Approved kan sättas

### Vy 2: "Schemalagt denna vecka"
- Visar Distribution-records där Publiceringsdatum ligger inom kommande 7 dagar
- Grupperat på Kanal

### Vy 3: "Performance senaste 30 dagar"
- Visar Distribution-records publicerade senaste 30 dagar
- Kolumner: Content (linked), Kanal, Impressions, Engagement, Konverteringar
- Sortera på Konverteringar descending

## Airtable MCP-koppling

För att Claude ska kunna läsa/skriva mot basen:

1. Hämta basens ID (format `appXXXXXXXXXXXXXX`) från Airtable URL
2. I Claude-konfigen, anslut Airtable MCP med personal access token (PAT) som har scope:
   - `data.records:read`
   - `data.records:write`
   - `schema.bases:read`
3. Verifiera med en list_tables_for_base-anrop mot basen
4. För veckogranskning: skapa en Skill som queryar Vy 1 och sammanfattar varje content-stycke

## Workflow för veckogranskning

1. Måndag morgon: Claude queryar Vy 1, listar allt i Review
2. Per content-stycke: Claude kollar mot brand-guidelines.skill.md + icp-summary.md
3. Claude flaggar avvikelser (förbjudna ord, fel ICP-segment, hook-rating < 3)
4. Användaren godkänner eller skickar tillbaka till Drafting
5. Approved-content schemaläggs i Distribution-tabellen

## Failure conditions

Säg ifrån om:

- Användaren vill skapa basen utan att ha icp-summary.md och brand-guidelines.skill.md — Content OS fungerar inte utan dom som referens
- Användaren vill lägga till >5 statusar — Status-fält ska vara enkla, inte ett Kanban-vetenskap
- Användaren vill att Claude ska auto-publicera — publicering är explicit-permission-action, sker manuellt

## References

- `growth-os-philosophy` — Content OS är ett konkret exempel på Growth OS-filer + verktyg
- `prompt-template-rmuk` — content-generering inuti basen sker via RMUK-promptar
