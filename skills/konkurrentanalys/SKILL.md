---
name: konkurrentanalys
description: AI-driven konkurrentanalys som levererar en strukturerad matris över 3-5 konkurrenter. Skrapar hemsida (positioning/pricing/features), Meta Ads Library (annonsvinklar) och LinkedIn (anställda-tillväxt, jobbannonser, posts) via Firecrawl-MCP, och bygger `konkurrent-matris.md` med en explicit "Lucka"-kolumn — där pengarna finns. Använd skillen varje gång André skriver "kör konkurrentanalys", "analysera konkurrenter", "kolla vad [företag] gör", eller jobbar med Modul 2 L2.3 (konkurrentanalys-lektionen). Skillen aktiveras också proaktivt när André bygger positionering, GTM-strategi eller ICP-spec och behöver kategori-kontext. Schemalagd diff-variant kan köras varje vecka via scheduled-tasks-MCP för att flagga ändringar sedan senaste körning. Outputen ska användas som input till `multi-agent-consensus` när André sedan formulerar sin positionering.
---

# konkurrentanalys

Skill för att bygga en konkurrent-matris som faktiskt informerar GTM-beslut — inte en akademisk "competitive landscape"-PDF. Varje fält i matrisen ska kunna kopplas till ett beslut: vad ska vi säga, vad ska vi inte säga, var finns gapet.

## Vad denna skill ÄR till för

- En-gångs konkurrentanalys för ett specifikt företag/Modul 2 L2.3-lektionen
- Bygga `konkurrent-matris.md` som artefakt i Project (för senare referens)
- Schemalagd vecko-diff: vad har konkurrenterna ändrat sen senast
- Input till positioneringsbeslut — körs innan `multi-agent-consensus` på position

## Vad denna skill INTE gör

- Marknadsstorlek / TAM-research → vanlig research-skill eller NotebookLM
- ICP-research → `icp-researcher` / `icp-builder`
- Voice-research på konkurrenter → `voice-of-customer`
- Formulera vår egen positionering → `multi-agent-consensus` (denna skill levererar input till den)

## Triggers — när skillen aktiveras

Skillen körs när André skriver något av:

- "kör konkurrentanalys [för X]"
- "analysera konkurrenter [för Y-marknaden]"
- "kolla vad [företag/lista] gör"
- "uppdatera konkurrent-matrisen"
- "diff sedan förra konkurrentanalysen"

Skillen körs också proaktivt (efter att Claude flaggat det och André sagt ok) när:

- André håller på att bygga positionering utan att ha kollat kategorin på 3+ månader
- André bygger Modul 2-innehåll och behöver konkret kategori-data
- André bygger GTM för en ny produkt/paket

## Vad du behöver innan du börjar

1. **ICP-spec** — `icp-summary.md` från Project. Skillen läser denna för att veta vilka konkurrenter som är relevanta (samma ICP, inte bara samma produktkategori).
2. **Företagsnamn eller kategori** — antingen ger André 3-5 specifika namn, eller en kategori-beskrivning ("svenska Claude-kurser för marknadsförare") som skillen själv hittar konkurrenter inom.
3. **Firecrawl-MCP** tillgänglig (`mcp__firecrawl__*`). Om inte — flagga och avbryt.
4. **(Valfritt) SEMrush/Ahrefs-MCP** för sökord-kolumnen. Om inte tillgänglig — hoppa över den kolumnen, flagga som "ej tillgänglig".
5. **Senaste matris** om det är en diff-körning. Default-plats: `marketing/konkurrent-matris.md` i Project.

## Workflow — steg-för-steg

### Steg 1: Identifiera konkurrenter (om inte givna)

Om André bara har gett en kategori-beskrivning:

1. Läs `icp-summary.md` — vilka söker ICP efter när dom letar lösning på den smärta vi adresserar?
2. Använd `firecrawl_search` för att hitta 8-12 potentiella konkurrenter
3. Filtrera till 3-5 som faktiskt delar ICP-overlap (samma målgrupp, inte bara samma produkt-kategori)
4. Visa listan för André och få ok innan du börjar skrapa

Om André har gett 3-5 specifika namn — hoppa direkt till Steg 2.

### Steg 2: Skrapa hemsida per konkurrent

För varje konkurrent, använd `firecrawl_scrape` mot deras hemsida + pricing-sida + features/product-sida. Detaljerade prompts finns i `references/firecrawl-prompts.md` — sektion "Hemsida".

Extrahera:

- **Positionering** — H1 + sub-headline + en mening om vad dom säger dom gör. INTE deras feature-lista, inte "vad jag tycker". Vad SÄGER dom?
- **Target** — vem säger dom att produkten är till för? Quote direkt från sidan.
- **Pricing** — exakta siffror om publikt, annars "ej publikt" eller "demo-only".
- **Features** — top 5 funktioner som lyfts på hemsidan (inte deras hela product-roadmap).

Om en sida har anti-scraping → flagga och hoppa till nästa konkurrent. Kör inte runt skydd.

### Steg 3: Meta Ads Library per konkurrent

För varje konkurrent, scrape Meta Ads Library (https://www.facebook.com/ads/library/). Detaljerade prompts: `references/firecrawl-prompts.md` — sektion "Meta Ads".

Extrahera:

- **Top 3 dominerande ad-vinklar** — vad är hooken? Smärta? Outcome? Social proof? Demo?
- **Format-mix** — video vs statisk vs karusell, ungefärligt
- **Aktiva annonser nu** — antal körda annonser senaste 30 dagarna (proxy för spend)

Om konkurrent inte kör Meta-ads alls → notera "ingen ads-närvaro" — det är också data.

### Steg 4: LinkedIn-företagsida per konkurrent

För varje konkurrent, scrape LinkedIn-företagsidan. Detaljerade prompts: `references/firecrawl-prompts.md` — sektion "LinkedIn".

Extrahera:

- **Anställda-tillväxt** — antal anställda nu vs 6 månader sen om synligt. Riktning (växande/krympande).
- **Jobbannonser** — vilka roller rekryterar dom? GTM-roller (säljare, marketing, customer success) säger mest om strategi.
- **Posts senaste 30 dagarna** — top 3 ämnen dom postar om. Kategori-tema.

### Steg 5: (Valfritt) SEO-data

Om SEMrush/Ahrefs-MCP tillgänglig:

- Top 10 sökord dom rankar på
- Estimerad organisk trafik
- Top 3 landing pages

Om inte — hoppa, flagga som "ej tillgänglig" i matrisen.

### Steg 6: Identifiera luckor

Detta är hela poängen med skillen. När alla 3-5 konkurrenters data är insamlad — leta efter:

1. **Vinklar som ingen säger** — tema/smärta/outcome som ICP bryr sig om (från `icp-summary.md`) men ingen konkurrent adresserar
2. **Format/kanal-gap** — kanaler där konkurrenter inte är aktiva men ICP är
3. **Pricing-gap** — pris-nivåer eller paketering som ingen täcker

Identifiera 3 sådana luckor. Inte 1, inte 10. Tre konkreta, formulerade som möjligheter — inte som observationer.

### Steg 7: Bygg matrisen

Skriv `konkurrent-matris.md` i exakt formatet nedan. Spara i `marketing/konkurrent-matris.md` om André bett om filskrivning, annars leverera som markdown i chatten.

## Output-format

```markdown
# Konkurrent-matris — [marknad/kategori]

**Körd:** [YYYY-MM-DD]
**ICP:** [1-mening från icp-summary.md]
**Konkurrenter analyserade:** [Företag 1, Företag 2, ...]
**Källor:** Hemsida (Firecrawl), Meta Ads Library, LinkedIn. [SEO via SEMrush — om tillämpligt]

---

## Matrisen

| Aspekt | [Företag 1] | [Företag 2] | [Företag 3] | [Företag 4] | [Företag 5] |
|---|---|---|---|---|---|
| **Positionering** (1 mening) | ... | ... | ... | ... | ... |
| **Target** (vem) | ... | ... | ... | ... | ... |
| **Pricing** | ... kr/mån, ... kr setup | Ej publikt | ... | ... | ... |
| **Features (top 5)** | 1. ... 2. ... 3. ... 4. ... 5. ... | ... | ... | ... | ... |
| **Ad-vinklar (top 3)** | 1. ... 2. ... 3. ... | ... | ... | ... | ... |
| **LinkedIn-tillväxt** | +X anställda / 6 mån | ... | ... | ... | ... |
| **LinkedIn-rekrytering** | [GTM-roller] | ... | ... | ... | ... |
| **Sökord-tema** | [om SEO-data tillgänglig] | ... | ... | ... | ... |

---

## Luckor — där pengarna finns

<span style="background-color: #FF6B5C; color: white; padding: 2px 6px;">**LUCKA 1:** [En mening]</span>

**Varför detta är en lucka:** [2-3 meningar — vilken ICP-smärta detta täcker, varför konkurrenterna inte adresserar den, hur stor möjligheten är]

**Hur vi skulle utnyttja den:** [1-2 meningar — konkret formulering, kanal, eller pricing-move]

---

<span style="background-color: #FF6B5C; color: white; padding: 2px 6px;">**LUCKA 2:** [En mening]</span>

**Varför detta är en lucka:** [...]

**Hur vi skulle utnyttja den:** [...]

---

<span style="background-color: #FF6B5C; color: white; padding: 2px 6px;">**LUCKA 3:** [En mening]</span>

**Varför detta är en lucka:** [...]

**Hur vi skulle utnyttja den:** [...]

---

## Observationer (icke-luckor men relevant)

[3-5 bullets med saker som inte är luckor men som påverkar GTM — t.ex. "Konkurrent 2 rekryterar 3 säljare = pivot till outbound", "Konkurrent 4 har sänkt pris 30% = press på prisnivå", "Alla 5 kör samma ad-vinkel = trolig kategori-trötthet"]

---

## Diff sedan förra körningen ([förra-datum])

[Om detta är en diff-körning. Annars: "Första körning — ingen diff."]

- **Vad är nytt:** [bullets]
- **Vad har ändrats:** [bullets]
- **Vad har försvunnit:** [bullets]

---

## Self-check (obligatorisk)

- [ ] 3-5 konkurrenter analyserade, alla med ICP-overlap (inte bara produkt-kategori-överlap)
- [ ] Varje cell i matrisen har data eller explicit "ej tillgänglig" (inga blanka)
- [ ] Positionering är quote eller paraphrase från konkurrent — inte min tolkning
- [ ] Pricing är exakta siffror eller explicit "ej publikt"
- [ ] Ad-vinklar är max 3 ord per vinkel — inte fullskaliga beskrivningar
- [ ] Exakt 3 luckor formulerade (inte 1, inte 5)
- [ ] Varje lucka har "varför" + "hur vi utnyttjar"
- [ ] Inga AI-tells i analystexten
- [ ] Inga förbjudna ord (revolutionerande, transformera, unlock, synergier, game-changer, Strategy Layer, trick-formuleringar)
- [ ] Inga emojier
- [ ] Diff-sektion fyllt (eller markerad "första körning")
- [ ] Filen sparad i `marketing/konkurrent-matris.md` om André bett om filskrivning
```

## Voice-regler

Konkurrent-matrisen är ett internt arbetsdokument — men skriv ändå som om André själv hade skrivit det. Korta meningar. Konkret. Ingen "som vi kan se från ovanstående analys".

### Skriv så här

- Konkret: "Konkurrent 2 har sänkt pris från 4 990 till 3 490 kr/mån sedan mars"
- Specifik: "Top ad-vinkel: byrå-frustration"
- Direkt: "Detta är en lucka. Ingen säger det. Vi borde."

### Skriv aldrig så här

- "Det är intressant att notera att..." → bara säg observationen
- "Konkurrent X verkar ha en stark position på marknaden" → vag, säg vad du faktiskt ser
- "Det finns potential att utforska..." → finns det en lucka eller inte?
- "Vidare kan vi observera att..." → bara skriv nästa mening

### Förbjudna ord

Samma lista som `multi-agent-consensus`:

- **AI-tells:** "i den här", "låt oss", "viktigt att förstå", "sammanfattningsvis"
- **Hype:** "revolutionerande", "spelförändrande", "10x", "transformera", "unlock", "synergier", "game-changer"
- **Krystade fraser:** "trick-formuleringar", "mental karta", "mindset-skifte", "Strategy Layer"

## Schemalagd diff-variant

Skillen kan köras schemalagt via `scheduled-tasks`-MCP (`mcp__scheduled-tasks__create_scheduled_task`). Setup:

- **Frekvens:** veckovis (default måndag morgon) eller månadsvis
- **Input:** samma konkurrent-lista som senaste körningen
- **Output:** skriver ny `konkurrent-matris.md` + en separat `konkurrent-diff-[YYYY-MM-DD].md` med bara skillnaderna
- **Notifiering:** flaggar i Project om luckor förändrats eller om konkurrent gjort signifikant move

Schemalagd version körs autonomt — men om en diff visar något substantiellt (priser ändrade >15%, ny positionering, GTM-pivot) ska skillen flagga till André direkt, inte vänta tills André öppnar filen.

## Workflow — så här kör du skillen

1. **Klargör scope.** Specifika konkurrenter eller kategori-baserad upptäckt? Ny analys eller diff? Hur många konkurrenter (3-5)?

2. **Läs `icp-summary.md`.** Du behöver veta ICP för att veta vilka konkurrenter som är relevanta och vilka luckor som faktiskt är luckor.

3. **Läs `references/firecrawl-prompts.md`.** Innehåller exakta prompts per skrapnings-uppgift (hemsida, Meta Ads, LinkedIn). Använd dom — improvisera inte Firecrawl-prompts.

4. **Identifiera konkurrenter** (om inte givna). Sök med Firecrawl, filtrera mot ICP, få ok från André innan skrapning.

5. **Skrapa parallellt.** För varje konkurrent: hemsida, Meta Ads, LinkedIn. Använd Firecrawl-MCP. Om en källa inte tillgänglig — flagga, hoppa, fortsätt.

6. **Bygg matrisen.** Cell för cell. Inga blanka — antingen data eller explicit "ej tillgänglig".

7. **Identifiera 3 luckor.** Inte 1, inte 5. Tre. Konkreta. Med "varför" och "hur vi utnyttjar".

8. **Kör self-check.** Punkt för punkt.

9. **Leverera.** Som `marketing/konkurrent-matris.md` om André bett om filskrivning. Annars markdown i chatten.

10. **(Om diff-körning)** Jämför mot senaste matris. Skriv `konkurrent-diff-[datum].md` med skillnaderna. Flagga substantiella moves till André.

## När det krånglar

- **"Konkurrenten har anti-scraping."** Hoppa. Försök inte kringgå skydd. Notera "anti-scraping, ej tillgänglig" i matrisen — det är data också.
- **"Meta Ads Library har inga ads för konkurrenten."** Det betyder antingen ingen ads-närvaro (notera "ingen ads-närvaro") eller dom kör annan kanal. Kolla LinkedIn-ads eller Google Ads transparancy center som backup.
- **"Jag hittar inga luckor."** Du har antingen valt konkurrenter som är för olika varandra (då är allt en lucka) eller för lika oss (då finns inga gap). Återgå till ICP — vad bryr sig ICP om som ingen konkurrent SÄGER på sin hemsida? Det är ofta luckan.
- **"5 konkurrenter säger exakt samma sak."** Det är inte ett problem — det är data. Kategori-trötthet är en lucka i sig. Skriv det som Lucka 1.
- **"André ber om 10 konkurrenter."** Nej. 3-5. Mer än 5 → matrisen blir oläsbar och luckor blir vaga. Fråga vilka 5 som är viktigast.
- **"Firecrawl ger gibberish tillbaka."** Förmodligen JS-renderad sida. Använd `firecrawl_scrape` med `formats: ["markdown"]` och vänta på render. Detaljer i `references/firecrawl-prompts.md`.

## Reference-filer

- `references/firecrawl-prompts.md` — Exakta Firecrawl-prompts per skrapnings-uppgift (hemsida, Meta Ads Library, LinkedIn). Inkluderar fallback-strategier för anti-scraping och JS-renderade sidor.
