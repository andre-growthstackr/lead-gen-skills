# Källval för list-building-wrapper

Detaljerad mappning mellan ICP-typ och rätt lead-källa. Läs detta varje gång `list-building-wrapper` ska välja källa.

## Beslutsträd

1. **Är ICP:n B2B SaaS, tech, eller startup-relaterad?**
   → **Apify** (LinkedIn Sales Navigator-scraping, Crunchbase, ProductHunt)

2. **Är ICP:n local services, retail, restauranger eller fysiska företag med geografisk koppling?**
   → **Google Maps** (Apify Google Maps Scraper actor)

3. **Är ICP:n generic B2B med behov av email + firmografi i samma stund?**
   → **Apollo.io** (databas med kontaktdata inbyggt)

4. **Behöver du strukturerad data snabbt, mindre research-tid, mer "färdigserverat"?**
   → **Aleads** (API som returnerar pre-strukturerad data)

5. **Otydligt?** → Fråga André. Gissa inte.

## Källa 1: Apify

### Passar när

- ICP är B2B SaaS, tech, fintech, martech, devtools
- Decision-maker har LinkedIn-närvaro (gäller 95% av tech-roller)
- Du letar specifik titel + geografi
- Behöver data utöver kontaktinfo (jobbannonser, posts, profil-fält)

### Apify actors att använda

- **LinkedIn Sales Navigator Scraper** — för title-search
- **LinkedIn Posts Scraper** — för intent-signaler (post-aktivitet)
- **Crunchbase Scraper** — för funding-data, anställda
- **Google Search Scraper** — för "site:linkedin.com/in/ + role + geo"

### Output

JSON med rik data per lead. Mappa till Airtable-kolumner i steg 6.

### Hård gräns

Max 1 000 results per actor-run för rate-limit-skydd. Default 100.

---

## Källa 2: Google Maps

### Passar när

- ICP är local services (tandläkare, frisörer, restauranger, gym)
- ICP är retail med fysisk adress
- Geografin är begränsad (en stad, en region)
- Email är inte primärt — telefon + hemsida räcker

### Apify actor

**Google Maps Scraper** — sök på keyword + location, returnerar:

- Företagsnamn
- Adress
- Telefon
- Hemsida
- Rating + reviews
- Öppettider

### Output

CSV med kolumner ovan. Email saknas oftast — kör Waterfall i L5.2 för att fylla via hemsida-scraping (Firecrawl).

### Hård gräns

Max 500 results per körning. Default 100.

---

## Källa 3: Apollo.io

### Passar när

- ICP är generic B2B (HR-tech, consulting, agenturer, traditional B2B)
- Du vill ha email på en gång (Apollo har inbyggd email-databas)
- Decision-maker-titlar är standardiserade
- Behöver firmografi (revenue, anställda, tech stack) som filter

### Filter att sätta

- Title (matcha exakt mot ICP decision-maker)
- Industry (välj 1-3 SIC-koder eller industries)
- Location (Sverige? Norden? Specifik stad?)
- Company size (matcha mot ICP)
- (Optional) Technologies — om ICP har tech stack-krav

### Output

CSV med email + firmografi inbyggt. Snabbaste vägen till en användbar lista, men kräver Apollo-prenumeration.

### Hård gräns

Apollo har egna export-limits per plan. Default 100 per körning.

---

## Källa 4: Aleads

### Passar när

- Du behöver strukturerad data nu, ingen tid för konfiguration
- Mindre tech-savvy användare
- B2B-data men inte hyperspecifik tech-vertikal

### Workflow

API-call med ICP-kriterier som parameter → får strukturerad output. Mindre rik än Apify, mindre djup än Apollo, men snabbast.

### Output

JSON med standardiserade kolumner. Importera direkt till Airtable utan transformation.

### Hård gräns

Per Aleads-plan, default 100.

---

## Övergripande regler

1. **En källa per körning.** Blanda inte. Det skapar dubblett-problem och datainkonsistens.
2. **Två körningar på olika källor = OK** om du dedupliterar på `linkedin_url` eller `email + company` mellan dem.
3. **Om ICP-täckning är låg på en källa** (t.ex. <30 leads) — kör en annan källa istället för att sänka kvalitetskraven.
4. **Logga alltid `source`-kolumnen** så L5.2-berikning kan prioritera waterfall-steg baserat på vad som redan finns.

## Anti-patterns

- Köra Apollo som default när ICP är local services (overkill, mest tomma kontakter)
- Köra Google Maps för SaaS-ICP (för få träffar med rätt titel)
- Köra Aleads när du behöver djup intent-data (för grund)
- Köra alla fyra källor "för säkerhets skull" (dubbletter, datainkonsistens, slöseri)
