---
name: voc-research
description: Kör Voice of Customer-mining från publika källor (G2/Capterra/Trustpilot 1-2-stjärniga reviews, Reddit-trådar, YouTube-kommentarer) via Firecrawl-MCP. Extraherar topp-5 pain points med 3 ordagranna citat per punkt, värdeerbjudande som faktiskt landar, och vanliga invändningar. Volymregel: minst 100 datapunkter innan slutsats. Genererar `voc-rapport.md` plus `voice-of-customer.skill.md` (en återanvändbar Skill som kodifierar kundens språkmönster för framtida copy-jobb). Använd den varje gång användaren skriver "kör VoC", "voice of customer research", "VoC-mining", eller hänvisar till Modul 2 lektion L2.2. Använd den även om användaren ber om "hitta vad kunderna säger" eller "research konkurrenternas reviews" i ett B2B-kontext.
---

# voc-research

Skill för empirisk Voice of Customer-mining. Lektion L2.2 i Claude för leadsgenerering. Output är två filer — en research-rapport och en återanvändbar copy-skill som låter framtida copy-jobb plocka från riktig kund-svenska.

## Vad denna skill gör

- Hämtar 1-2-stjärniga reviews från G2, Capterra, Trustpilot för konkurrenters produkter
- Hämtar relevanta Reddit-trådar och YouTube-kommentarer
- Kräver minst 100 datapunkter innan slutsatser dras
- Extraherar topp-5 pain points med 3 ordagranna citat per punkt
- Genererar `voc-rapport.md` och `voice-of-customer.skill.md`

## Vad denna skill INTE gör

- Bygger inte ICP → använd `icp-builder`
- Skriver inte färdig copy → använd output:en (voice-of-customer.skill.md) i framtida copy-jobb
- Hittar inte på citat — bara ordagranna utdrag från riktiga källor

## Triggers

Aktivera skillen när användaren skriver något av följande:

- "kör VoC"
- "voice of customer research"
- "VoC-mining"
- "hitta vad kunderna säger"
- "research konkurrenternas reviews"
- "starta L2.2"

Aktivera även när användaren skriver copy och nämner att de "saknar kundens språk" eller "låter generiskt".

## Workflow

### Steg 1 — Hämta ICP eller be om input

Kolla först om `icp-summary.md` finns i nuvarande working directory eller i Claude Project. Om ja — läs den och plocka ut:

- Bransch + segment
- Produkt-kategori (vad de köper, t.ex. "marketing automation", "CRM", "lead gen-byrå")
- 3-5 konkurrent-namn eller produkt-namn de jämför med
- Den mätbara smärtan (Fråga 7 från ICP-interviewn)

Om `icp-summary.md` saknas — be användaren om:
1. Vilken produkt-kategori du säljer (t.ex. "B2B marketing automation")
2. 3-5 konkurrenter eller jämförbara produkter
3. Vilken målgrupp (CMO, Head of Growth, etc.)

Hitta inte på konkurrenter. Om användaren inte vet — säg ifrån.

### Steg 2 — Källista (planera scrapingen)

Innan du scrapar — bygg en lista över exakt vilka URL:er du ska hämta. Mål: 100+ datapunkter.

**G2 / Capterra / Trustpilot:**
- För varje konkurrent: hämta 1-stjärniga och 2-stjärniga reviews
- URL-mönster G2: `g2.com/products/[product-slug]/reviews?filters%5Bnps_score%5D%5B%5D=1&filters%5Bnps_score%5D%5B%5D=2`
- Capterra: `capterra.com/p/[id]/[name]/reviews/`
- Trustpilot: `trustpilot.com/review/[domain]`

**Reddit:**
- Använd `mcp__firecrawl__firecrawl_search` med queries som "[konkurrent] alternative reddit", "[produkt-kategori] frustrating", "switching from [konkurrent]"
- Relevanta subreddits: r/marketing, r/SaaS, r/sales, r/PPC, r/bigseo, r/B2BMarketing
- Hämta topp-trådar (50+ upvotes) plus kommentarstrådar

**YouTube-kommentarer:**
- Sök efter videos: "[konkurrent] review", "[produkt-kategori] honest review"
- Scrapa kommentarsektionen via Firecrawl

**Volymregel:**
Räkna datapunkter. En "datapunkt" = en review, en Reddit-kommentar, en YouTube-kommentar. **Stoppa inte under 100.** Om du når 100 från en kanal — fortsätt ändå tills du har minst 30 från varje av minst 3 olika källor (G2/Capterra/Reddit). Diversitet > volym efter 100.

### Steg 3 — Scrapa via Firecrawl-MCP

Använd verktygen i denna ordning:

1. **`mcp__firecrawl__firecrawl_map`** — Hitta alla review-undersidor för en produkt
2. **`mcp__firecrawl__firecrawl_scrape`** — Hämta enskilda sidor (markdown-format)
3. **`mcp__firecrawl__firecrawl_search`** — För Reddit + YouTube-discovery
4. **`mcp__firecrawl__firecrawl_extract`** — För strukturerad data (rating + body + datum)

Spara råmaterial som du går — skapa `voc-raw/` med en fil per källa så att citaten kan verifieras tillbaka.

Om Firecrawl är otillgänglig — säg ifrån. Faka inte VoC-data. Manuell version: be användaren paste:a in 100+ reviews/kommentarer själv.

### Steg 4 — Klustering av pain points

Med 100+ datapunkter — gruppera teman. Använd Claude för att klustra, men citaten ska vara ordagranna.

Mål: 5 distinkta pain-point-kluster, varje med:
- En koncis namn på smärtan (1-5 ord)
- Frekvens (hur många datapunkter nämner detta)
- 3 ordagranna citat (med källa: G2/Reddit/YouTube + datum om tillgängligt)

Om någon pain-point bara har 1-2 datapunkter — den hör inte hemma i topp-5. Slå ihop eller stryka.

### Steg 5 — Identifiera värdeerbjudande som funkar

Vänd på reviews — leta efter 4-5-stjärniga som beskriver "vad gör att det funkar". Det är värdeerbjudandet du ska efterlikna (eller överträffa).

Format: 1-3 outcome-fraser kunderna använder, ordagranna.

### Steg 6 — Vanliga invändningar

Leta i lower-star reviews efter konkreta köpinvändningar:
- Pris ("for ett verktyg som inte gör X — overpriced")
- Onboarding ("tog 6 veckor att komma igång, lovade 1 vecka")
- Integration ("integrerar inte med Pipedrive trots vad de sa")
- Support ("kund-support svarar på engelska, jag är fransk")

3-5 invändningar med citat.

### Steg 7 — Generera `voc-rapport.md`

Spara i nuvarande working directory eller där användaren bett om. Strukturen finns i Output-format nedan.

### Steg 8 — Generera `voice-of-customer.skill.md`

Detta är en SEPARAT SKILL-fil som kodifierar kundens språkmönster så att framtida copy-jobb kan hämta från riktig kund-svenska (inte AI-svenska).

Strukturen finns i Output-format nedan.

## Output-format

### Fil 1: `voc-rapport.md`

```markdown
# Voice of Customer-rapport: [ICP / produkt-kategori]

**Datum:** [YYYY-MM-DD]
**Antal datapunkter:** [N — ska vara ≥100]
**Källor:** [G2: X reviews, Capterra: Y, Reddit: Z trådar, YouTube: W kommentarer]
**Konkurrenter analyserade:** [lista]

---

## Topp-5 Pain Points

### 1. [Smärt-namn] — [N datapunkter]

> "[Ordagrant citat 1]" — [Källa, datum]

> "[Ordagrant citat 2]" — [Källa, datum]

> "[Ordagrant citat 3]" — [Källa, datum]

**Mönster:** [1-2 meningar om vad citatet säger oss]

### 2. [Smärt-namn] — [N datapunkter]
[Samma struktur]

### 3-5. [...]

---

## Värdeerbjudande som faktiskt funkar

Vad kunderna säger när det landar (från 4-5-stjärniga reviews):

> "[Ordagrant citat 1]"
> "[Ordagrant citat 2]"
> "[Ordagrant citat 3]"

**Outcome-fraser att efterlikna:**
- "[fras 1]"
- "[fras 2]"
- "[fras 3]"

---

## Vanliga invändningar

### Invändning 1: [Namn]

> "[Citat]" — [Källa]

**Hur du bemöter:** [1-2 meningar — låt det här ledas av användarens egen positionering, men ge förslag]

### Invändning 2-5: [...]

---

## Råmaterial

Sparat i `voc-raw/` — fil per källa. Citaten ovan kan verifieras tillbaka mot råfilerna.
```

### Fil 2: `voice-of-customer.skill.md`

Detta är en återanvändbar Skill — andra prompts kan plocka från den. YAML frontmatter + sektioner som specifikt hjälper copy-generering.

```markdown
---
name: voice-of-customer
description: Kodifierar kundens språkmönster för [produkt-kategori / ICP]. Använd den i alla copy-jobb (LinkedIn-posts, ads, email-sekvenser, landningssidor) för att låta som kunden själv pratar istället för som AI eller marketing-byrå. Hämta fraser, motfraser och invändning-bemötanden härifrån — hitta inte på.
---

# voice-of-customer: [ICP / produkt-kategori]

Källa: VoC-rapport [datum], baserat på [N] datapunkter från G2/Capterra/Reddit/YouTube.

## Fraser kunderna faktiskt använder (utfall)

- "[fras 1 — outcome-formulerad, ordagrann från review]"
- "[fras 2]"
- "[fras 3]"
- "[fras 4]"
- "[fras 5]"

## Fraser kunderna använder för att beskriva smärtan

- "[ordagrann smärt-fras 1]"
- "[fras 2]"
- "[fras 3]"
- "[fras 4]"
- "[fras 5]"

## Ord att UNDVIKA (känns som marketing-bullshit för denna ICP)

- [ord 1 — t.ex. "synergier"]
- [ord 2 — t.ex. "transformation"]
- [ord 3]

## Invändning → bemötande

| Invändning (ordagrann) | Hur copy ska adressera |
|---|---|
| "[invändning 1]" | [bemötande — kort, konkret] |
| "[invändning 2]" | [bemötande] |
| "[invändning 3]" | [bemötande] |

## Hur denna skill används

I framtida copy-prompts — referera direkt: "Använd voice-of-customer-skillen och plocka 2-3 fraser ordagrant från sektionen 'Fraser kunderna använder för att beskriva smärtan'."

Hitta inte på nya fraser. Om något fattas — kör en ny VoC-mining via `voc-research`.
```

## Failure conditions

Säg ifrån istället för att leverera på lös basis när:

- **Du har <100 datapunkter.** Volymregeln finns för att VoC blir slumpartad under det. Säg "Jag har bara N datapunkter — jag behöver minst 100 innan jag kan dra slutsatser. Vill du att jag fortsätter scrapingen, eller har du fler källor?"
- **Firecrawl returnerar fel på 3+ källor.** Säg ifrån. Föreslå manuell paste-in eller annan källa.
- **Alla citat kommer från samma källa.** Diversitet > volym. Säg "100 citat från G2 är inte VoC — det är G2-användares åsikt. Jag behöver Reddit eller YouTube-kommentarer också."
- **Användaren ber dig "polera" citat.** Citat ska vara ordagranna. Säg "Citaten är som folk faktiskt skriver. Att polera dem skulle göra dem mindre värda — de är kraftiga för att de inte är marketing-svenska."

## References

- `references/källor-per-bransch.md` — Var du hittar VoC för olika ICPs (SaaS / byrå / industri / e-handel)
- `references/scraping-mönster.md` — Firecrawl-mönster för G2/Capterra/Reddit/YouTube + workaround när siter blockerar
