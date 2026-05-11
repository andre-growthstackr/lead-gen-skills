# Content OS — Airtable schema

Schema-spec för Content OS-basen som content-ideation-skillen skriver till. Basen byggs av användaren — denna spec beskriver exakt vilka fält, relations och views som behövs.

## Översikt

Basen heter **Content OS** och har tre tabeller plus en valfri stödtabell.

| Tabell | Syfte | Antal records (typ) |
|---|---|---|
| Idéer | Råa idéer som väntar på godkännande | ~100/månad |
| Content | Godkända idéer i produktion | ~28/månad |
| Distribution | En record per plattform per content-piece | ~80/månad |
| Smärtor (valfri) | ICP-smärta-bibliotek för dropdown | 5-15 |

## Tabell 1: Idéer

Alla idéer landar här först. Status-fältet styr om de går vidare till Content.

| Fält | Typ | Notering |
|---|---|---|
| Idé-ID | Auto-number | Primary key |
| Hook | Single line text | Max 15 ord |
| Content-typ | Single select | SFV / Image / LFV / Newsletter |
| Vinkel | Long text | 1-2 meningar |
| Body-outline | Long text | 3-5 punkter |
| CTA | Single line text | 1 mening |
| Distribution | Multiple select | LinkedIn, IG Reels, IG Feed, YT Shorts, YT, Newsletter, LinkedIn-artikel |
| ICP-smärta | Link to "Smärtor" | Multi-select om flera smärtor täcks |
| VoC-citat | Long text | Direkt citat med persona-attribuering |
| Källa | Long text | NotebookLM-källa / blogginlägg / egen erfarenhet |
| Status | Single select | Förslag / Godkänd / Pausa / Skrota |
| Vecka | Number | 1-4 för månadens plan |
| Skapad | Created time | Auto |
| Av | Created by | Auto |
| Kommentar André | Long text | För feedback under granskning |

### Rekommenderade views

- **Väntar granskning** — filter: Status = Förslag, sort: Skapad desc
- **Godkänd, väntar produktion** — filter: Status = Godkänd, sort: Vecka asc
- **Per content-typ** — group by: Content-typ

## Tabell 2: Content

Idéer som är godkända flyttas hit (eller länkas — välj en konvention och håll).

| Fält | Typ | Notering |
|---|---|---|
| Content-ID | Auto-number | Primary key |
| Titel | Single line text | Slutgiltig titel |
| Content-typ | Single select | Ärvs från Idéer |
| Från idé | Link to "Idéer" | Källa |
| Manus/copy | Long text | Färdig copy |
| Status | Single select | Produktion / Granskning / Klar för publish / Publicerad |
| Produktionsdatum | Date | När den ska filmas/skrivas |
| Publiceringsdatum | Date | När den går live (per plattform i Distribution) |
| Filer | Attachment | Råfiler, redigerade filer, thumbnails |
| Ansvarig | Collaborator | Vem som producerar |
| Distribution | Link to "Distribution" | Multi — en record per plattform |
| Resultat | Long text | Efter publish — engagement, leads, learnings |

### Rekommenderade views

- **Veckans produktion** — filter: Produktionsdatum = denna vecka
- **Klar för publish** — filter: Status = Klar för publish
- **Kalender** — calendar view på Publiceringsdatum

## Tabell 3: Distribution

En record per plattform per content-piece. Detta är granulärt — samma SFV som går till LinkedIn + Reels + Shorts blir 3 records.

| Fält | Typ | Notering |
|---|---|---|
| Distribution-ID | Auto-number | Primary key |
| Content | Link to "Content" | Tillbaka till parent |
| Plattform | Single select | LinkedIn, IG Reels, IG Feed, YT Shorts, YT, Newsletter, LinkedIn-artikel |
| Publiceringsdatum | Date | Specifikt för plattformen |
| Plattform-specifik copy | Long text | LinkedIn-text vs IG-caption vs YT-description |
| Status | Single select | Schemalagd / Publicerad / Performing / Done |
| Publik URL | URL | Länk till live-posten |
| Views / Impressions | Number | Manuell eller via integration |
| Engagement | Number | Likes + comments + shares |
| Leads/conversions | Number | Spårat via UTM eller manuell |
| Notering | Long text | Vad funkade, vad funkade inte |

### Rekommenderade views

- **Schemalagd veckan** — filter: Publiceringsdatum = denna vecka, Status = Schemalagd
- **Performing top 10** — sort: Engagement desc, limit 10
- **Per plattform** — group by: Plattform

## Tabell 4 (valfri): Smärtor

Stödtabell för ICP-smärta-bibliotek. Linkas från Idéer.

| Fält | Typ | Notering |
|---|---|---|
| Smärta | Single line text | Kort namn |
| Beskrivning | Long text | 2-3 meningar |
| VoC-citat | Long text | 3-5 exempel-citat |
| Persona | Multiple select | Vilka i ICP:n känner denna smärta |
| Frekvens | Single select | Topp / Vanlig / Periferi |

## Relations

```
Idéer ──< Content ──< Distribution
            │
            └── (Idéer länkar också till Smärtor)
```

## MCP-skapande

Om Airtable-MCP är ansluten kan skillen skapa basen automatiskt:

1. `mcp__8e100d6a-..._create_base` med namnet "Content OS"
2. För varje tabell ovan — `create_table` med rätt fält
3. `create_field` för varje fält enligt spec
4. `create_records_for_table` för att fylla i initial test-record

Om basen redan finns — använd `list_tables_for_base` + `get_table_schema` för att verifiera att schemat matchar, sedan `create_records_for_table` för att lägga in idéer.

## Notering

Detta schema är medvetet enkelt. När basen växer kan användaren lägga till:

- Performance-formler (Engagement / Views)
- Automations (när Status = Godkänd → notifiera Slack)
- Repurpose-länkar (LFV → SFV-derivat)

Men håll det enkelt i månad 1. Komplexitet dödar Content OS.
