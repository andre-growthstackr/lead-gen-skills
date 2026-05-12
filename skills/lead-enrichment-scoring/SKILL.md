---
name: lead-enrichment-scoring
description: Berika och poängsätt råleads (0-100) från Airtable Leads-tabell genom Apify-LinkedIn-data, Firecrawl-hemsida-scraping och Claude-LLM-bedömning mot ICP-kriterier. Aktiveras varje gång användaren skriver "berika leads", "scoring leads", "kvalificera leads", "poängsätt leads", "kör enrichment", "score leads" eller refererar till L5.2 i kursen. Endast leads med score > 70 markeras "kvalificerad" och går vidare till outreach. Skillen läser ICP-spec från Project, kör waterfall-berikning, bedömer firmografi/tech/intent/köpfönster och uppdaterar Airtable-rader med Score-fält + Berikning-fält. Använd den även när användaren bara säger "vilka leads ska jag kontakta" — skillen ÄR kvalificeringen.
---

# lead-enrichment-scoring

Skill för steg 2 i outbound-pipelinen: från råleads till kvalificerade leads. Hämtar leads från Airtable Leads-tabell (skapade av `list-building` i L5.1), berikar via Apify + Firecrawl, scorar 0-100 med Claude som domare, och markerar score > 70 som "kvalificerad". Bara dessa går vidare till L5.3 (LinkedIn) eller L5.4 (cold email).

## Vad skillen gör

- Läser obearbetade leads från Airtable Leads-tabell (`status = "ny"`)
- Läser ICP-kriterier från `icp-summary.md` i Project
- Berikar varje lead via Apify (LinkedIn) + Firecrawl (hemsida) + public APIs
- Kör Claude-LLM-bedömning mot fyra ICP-dimensioner
- Skriver Score (0-100) + motivering tillbaka till Airtable
- Markerar `status = "kvalificerad"` på leads med score > 70
- Markerar `status = "diskvalificerad"` på resten med kort orsak

## Vad skillen INTE gör

- Bygga rålistan → `list-building`
- Skicka outreach → `linkedin-outreach-cowork` eller `cold-email`
- Definiera ICP-kriterier → görs i Modul 2 (ICP research)

## Triggers

Aktivera när användaren skriver något av följande:

- "berika leads"
- "scoring leads"
- "kvalificera leads"
- "poängsätt leads"
- "kör enrichment"
- "score leads"
- "L5.2" eller "lektion 5.2"
- "kör waterfall berikning"

## Sekvens: diskvalificering → scoring → berikning

Ordningen spelar roll. Diskvalificera först med gratis data. Sen scora. Sen berika det som klarade scoring. Annars blöder du pengar på enrichment-API:er för leads som ändå faller bort.

### Steg 1: Hård diskvalificering (gratis)

Filtrera bort direkt med fält du redan har från L5.1:

- Land matchar inte ICP-geografi
- Företagsstorlek faller utanför ICP-band
- Bransch matchar inte ICP-vertikal
- Namn-fält är tomt eller uppenbart fel
- LinkedIn-URL saknas (för B2B-ICP)

Märk dessa `status = "diskvalificerad"` och `disqualification_reason = "<orsak>"`. De berikas aldrig.

### Steg 2: Berikning (waterfall)

För de som klarat steg 1, berika i waterfall:

**Lager 1: Apify LinkedIn**
- Anställda-räkning
- Senaste 5 posts (för intent-signaler)
- Jobbannonser publicerade senaste 30 dagarna
- Recent funding/news (om i headline)

**Lager 2: Firecrawl-hemsida**
- Hero-text + pricing-sida
- Tech stack (Wappalyzer-pattern på script-tags)
- "Om oss" → grundades-år, anställda
- Säljsidor / book-a-call-flow

**Lager 3: Public APIs** (där tillgängligt)
- Bolagsverket (org-nummer, antal anställda)
- LinkedIn company page-data (om åtkomst finns)

Skriv all berikning till `enrichment_data` (JSON) i Airtable.

### Steg 3: Scoring (Claude som domare)

Kör Claude-LLM-prompt mot berikad data med fyra dimensioner. Detaljerad rubric i `references/scoring-rubric.md`.

**Fyra scoring-dimensioner (varje 0-25 poäng, summa 0-100):**

| Dimension | Vad bedöms |
|---|---|
| **Firmographics match** | Företagsstorlek, bransch, geografi vs ICP |
| **Tech stack relevance** | Använder de tech som matchar din produkt? |
| **Intent-signaler** | Jobbannons, post-aktivitet, funding-news |
| **Köpfönster-indikatorer** | Säsong, kvartalsslut, ny VP, expansion |

Claude returnerar:
```json
{
  "firmographics": 22,
  "tech_stack": 18,
  "intent": 15,
  "buying_window": 10,
  "total": 65,
  "reasoning": "Två meningar motivering"
}
```

### Steg 4: Markera & filtrera

- `score > 70` → `status = "kvalificerad"` (går till L5.3/L5.4)
- `score 40-70` → `status = "nurture"` (long-term, ingen outreach nu)
- `score < 40` → `status = "diskvalificerad"`

## Workflow

### 1. Hämta råleads

Via Airtable MCP:
```
list_records_for_table där status = "ny"
```

### 2. Läs ICP-spec

```
Read icp-summary.md från Project
```

Extrahera kriterierna som strukturerade fält (geografi, storlek, vertikal, tech, decision-maker).

### 3. Loopa per lead

För varje lead:

a) Kör hård diskvalificering. Om fail → uppdatera Airtable med `status = "diskvalificerad"`, gå till nästa lead.

b) Kör waterfall-berikning (Apify → Firecrawl → public APIs).

c) Kör Claude scoring-prompt med ICP + berikad data som input.

d) Skriv `Score`, `enrichment_data`, `scoring_reasoning`, `status` till Airtable.

### 4. Sammanfattning

Vid loopens slut, rapportera:

```
78 leads bearbetade
- 18 hård-diskvalificerade (geografi/storlek)
- 22 score < 40 (diskvalificerade)
- 19 score 40-70 (nurture)
- 19 score > 70 (KVALIFICERADE — redo för outreach)

Top 3 kvalificerade:
1. Lina Andersson, VP Marketing @ Acme — 87 (recent funding + matching tech)
2. Erik Lund, Head of Growth @ Nordsoft — 81 (jobbannons + LinkedIn-aktivitet)
3. Sara Olsson, CMO @ Tindra — 76 (ICP-bullseye)

Nästa steg: linkedin-outreach-cowork eller cold-email.
```

## Output-format

Per lead, uppdaterade Airtable-fält:

- `Score` (0-100, integer)
- `Status` (ny | kvalificerad | nurture | diskvalificerad)
- `Enrichment_data` (JSON med all berikning)
- `Scoring_reasoning` (max 2 meningar motivering)
- `Disqualification_reason` (om diskvalificerad)
- `Enriched_at` (ISO timestamp)

## Failure conditions

- ICP-spec saknas → fråga André innan du börjar
- Apify-credits slut → falla tillbaka till bara Firecrawl + Claude-bedömning, logga som lägre konfidens
- Firecrawl timeout på enskild lead → skippa hemsida-fält men kör scoring på det som finns
- Airtable MCP nere → batcha lokalt i CSV, importera när MCP är tillbaka
- Score-fältet saknas i Airtable → skapa fältet via MCP innan loop

## Volym & kostnad

- Hård gräns 100 leads per körning (Apify-kostnad)
- Firecrawl-cap på 50 hemsidor per körning
- Om listan > 100: kör i batches om 50

## References

- `references/scoring-rubric.md` — Detaljerad 0-100 scoring-rubric per dimension med poäng-anchors
