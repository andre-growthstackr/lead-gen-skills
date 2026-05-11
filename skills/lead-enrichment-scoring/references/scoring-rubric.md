# Scoring-rubric för lead-enrichment-scoring

Detaljerad 0-100 scoring-rubric. Använd som input till Claude-LLM-prompten när varje lead ska bedömas. Fyra dimensioner, vardera 0-25 poäng. Summa = total score.

## Dimension 1: Firmographics match (0-25)

Hur bra matchar företagets baseline-data mot ICP:n?

| Poäng | Tolkning | Anchor |
|---|---|---|
| 25 | Bullseye | Geografi, storlek, vertikal — alla tre exakt på mål |
| 20 | Stark match | 2 av 3 exakt, en aning utanför på ena |
| 15 | OK match | Två av tre på mål, en tredje utanför |
| 10 | Svag match | Bara en dimension på mål |
| 5 | Marginell | Företaget finns men matchar knappt ICP:n |
| 0 | Ingen match | Borde inte ha kommit förbi hård diskvalificering |

### Bedömningskriterier

- **Geografi**: Sverige om ICP säger Sverige. Norden tillåtet om ICP säger Norden. EU tillåtet om ICP säger EU. Annars 0 på denna dimension.
- **Företagsstorlek**: inom ICP-bandet (anställda). 51-200 ICP = 45-220 OK (10% tolerans), utanför = 0.
- **Vertikal**: SaaS-ICP kräver SaaS, fintech, devtools, martech. Generic B2B-ICP är bredare.

## Dimension 2: Tech stack relevance (0-25)

Hur väl matchar deras tech stack mot vad din produkt bygger på eller integrerar med?

| Poäng | Tolkning | Anchor |
|---|---|---|
| 25 | Använder exakt teknologin som är prerequisite | Du säljer HubSpot-integration, de kör HubSpot |
| 20 | Använder närliggande teknologi | Du säljer HubSpot-integration, de kör Salesforce |
| 15 | Generic tech som är kompatibel | Modern SaaS-stack utan blockerare |
| 10 | Okänd tech stack | Hemsidan gav inget Wappalyzer-pattern |
| 5 | Möjligen inkompatibel | Legacy-tech eller direkt konkurrerande |
| 0 | Inkompatibel | Använder konkurrent som ersätter din produkt |

### Bedömningskriterier

- Wappalyzer-pattern från Firecrawl-scrape av hemsida
- Jobbannonser (tech-keywords i annonser = tech i bruk)
- LinkedIn-profil-fält (anställda nämner verktyg i bio)

Om ICP inte har tech stack-krav → default 15 (neutral).

## Dimension 3: Intent-signaler (0-25)

Visar leadet eller företaget tecken på att vara i marknad just nu?

| Poäng | Signal | Anchor |
|---|---|---|
| 25 | Flera färska signaler | Funding senaste 30 dgr + jobbannons + relevant post |
| 20 | Stark enskild signal | Funding-runda + matchande jobbannons |
| 15 | Måttlig signal | Aktiv på LinkedIn med relevanta posts senaste veckan |
| 10 | Svag signal | Postat något generiskt senaste månaden |
| 5 | Knappt aktiv | Sista posten > 3 månader sedan |
| 0 | Dödbox | Ingen aktivitet 6+ månader |

### Konkreta signaler att leta efter

- **Funding**: A-, B-, C-runda senaste 90 dagarna (Crunchbase/news)
- **Jobbannons**: anställer roll som matchar din produkts användare (t.ex. "Growth Manager" om du säljer growth-tooling)
- **Posts**: leadens senaste 5 LinkedIn-posts — postar de om problem din produkt löser?
- **Konferens-närvaro**: nämner kommande summit, mässa, event
- **Ny VP/Director**: ny ledare = ofta tech-stack-omvärdering
- **Press release**: produkt-lansering, expansion, ny region

## Dimension 4: Köpfönster-indikatorer (0-25)

Är det rätt tidpunkt att köpa, just nu?

| Poäng | Indikator | Anchor |
|---|---|---|
| 25 | Aktivt köpfönster | Nytt kvartal + budget-cykel + ny exec |
| 20 | Tydligt fönster | Kvartalsskifte + tillväxtfas |
| 15 | Sannolikt fönster | Företag i tillväxt, men ingen specifik trigger |
| 10 | Okänt fönster | Inga indikatorer åt något håll |
| 5 | Stängt fönster | Just gjort en stor investering nyligen |
| 0 | Hopplös tid | Just genomgått layoffs / kris |

### Konkreta indikatorer

- Kvartalsslut/budget-cykel i deras region
- Fiskalt år (många US-bolag = Jan–Dec, andra = Jul–Jun)
- Recent funding = pengar att spendera (06 mån fönster)
- Layoffs eller hiring freeze = stängt fönster
- Ny exec inom senaste 90 dgr (de bygger om stack)
- Säsongs-mönster i deras vertikal (retail = Q4, B2B SaaS = Q1)

## Totalsumma och tolkning

| Total | Status | Action |
|---|---|---|
| 80-100 | A-tier kvalificerad | Outreach prio 1, personlig, snabb |
| 70-79 | B-tier kvalificerad | Outreach prio 2, lätt research |
| 40-69 | Nurture | Long-term content, ingen outreach nu |
| 0-39 | Diskvalificerad | Inget mer arbete |

## Prompt-template för Claude-domare

Använd denna struktur när du kör scoring:

```
Du är scoring-domare för B2B-leads.

ICP-spec:
<icp-spec.md innehåll>

Lead-data:
<berikad lead-data som JSON>

Scoring-rubric:
<denna fil>

Returnera ett JSON-objekt med:
{
  "firmographics": <0-25>,
  "tech_stack": <0-25>,
  "intent": <0-25>,
  "buying_window": <0-25>,
  "total": <summa>,
  "reasoning": "<max 2 meningar varför totalen blev som den blev>"
}

Bedöm strikt mot rubric:en. Inga halvpoäng. Inga gissningar — om data saknas, default till mitten-värde och nämn det i reasoning.
```

## Anti-patterns

- Snälla bedömningar — bättre att vara strikt och slänga 30% av listan än att slösa outreach på lukewarm leads
- Halvpoäng (18.5) — håll dig till hela tal, rubric:en bygger på det
- Bedöma utan data — om enrichment saknades, returnera 10 (mitten) och flagga det i reasoning
- Lägga till en femte dimension "magkänsla" — strikt fyra dimensioner, annars går rubric:en sönder
