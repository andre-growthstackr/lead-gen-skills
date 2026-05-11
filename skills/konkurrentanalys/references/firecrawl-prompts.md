# Firecrawl-prompts per skrapnings-uppgift

Exakta prompts och parametrar för varje del av konkurrentanalysen. Använd dessa — improvisera inte. Konsistent skrapning gör diff-körningar meningsfulla.

---

## Sektion 1: Hemsida

### Mål

Extrahera positionering, target, pricing, top 5 features per konkurrent.

### URLs att skrapa per konkurrent

1. Startsidan (`/`)
2. Pricing-sidan (`/pricing`, `/priser`, `/plans` — testa alla varianter)
3. Product/features-sidan (`/product`, `/features`, `/platform`)
4. About-sidan (om publikt visar målgrupp)

### Firecrawl-anrop

Använd `mcp__firecrawl__firecrawl_scrape` med:

```
{
  "url": "<URL>",
  "formats": ["markdown"],
  "onlyMainContent": true,
  "waitFor": 2000
}
```

Om sidan är JS-renderad (få ord tillbaka, ingen H1 synlig) — använd istället `mcp__firecrawl__firecrawl_extract` med en schema-prompt:

```
{
  "urls": ["<URL>"],
  "schema": {
    "positioning_h1": "string — main H1 on the page",
    "positioning_subheadline": "string — first sub-headline under H1",
    "target_audience": "string — who they say the product is for (quote from page)",
    "primary_cta": "string — main CTA button text",
    "top_features": "array of strings — top 5 features mentioned on homepage"
  },
  "prompt": "Extract positioning, target audience, and top features from this homepage. Use exact text from page, not your interpretation."
}
```

För pricing-sidan, separat extract:

```
{
  "urls": ["<URL>/pricing"],
  "schema": {
    "plans": "array of objects with {name, price, currency, period, included_features}",
    "is_public": "boolean — is pricing publicly visible or demo-only",
    "free_trial": "boolean"
  }
}
```

### Vad du extraherar

- **Positionering** = H1 + sub-headline kombinerat till 1 mening. Quote eller paraphrase. Inte tolkning.
- **Target** = vem de SÄGER produkten är för. Quote från about- eller startsidan.
- **Pricing** = exakta siffror om publikt. Om "Contact us" → notera "ej publikt, demo-only".
- **Features** = top 5 från startsidan, INTE från full feature-listan. Vi vill veta vad dom prioriterar att kommunicera.

### Anti-scraping fallback

Om Firecrawl returnerar 403 eller Cloudflare-block:

1. Försök med `firecrawl_extract` istället för `scrape` (använder browser)
2. Om fortsatt blockerat → notera "anti-scraping, ej tillgänglig" i matrisen
3. Kringgå INTE skydd manuellt. Det är inte vår sak.

### Failure conditions

- Sida 404 eller inte hittad → notera "URL ej funnen"
- Pricing ej publikt → "Ej publikt — demo/sales-call only"
- Hemsida är på språk vi inte hanterar → notera språk, hoppa över

---

## Sektion 2: Meta Ads Library

### Mål

Top 3 dominerande ad-vinklar + format-mix + aktivitets-nivå per konkurrent.

### URL-mönster

```
https://www.facebook.com/ads/library/?active_status=active&ad_type=all&country=SE&search_type=page&q=[KONKURRENT_NAMN]
```

Country-parameter: använd "SE" för svensk marknad, "ALL" för bred.

### Firecrawl-anrop

Använd `firecrawl_extract` (NOT scrape — Meta är heavily JS-renderad):

```
{
  "urls": ["<META_ADS_LIBRARY_URL>"],
  "schema": {
    "advertiser_name": "string",
    "total_active_ads": "number",
    "ads": "array of objects with {ad_text_preview, format (video/image/carousel), call_to_action, started_running_date}"
  },
  "prompt": "Extract the active ads for this advertiser from Meta Ads Library. For each ad, capture the headline/primary text preview, format type, and CTA. Get at least the top 10 if available."
}
```

### Vad du extraherar

- **Top 3 ad-vinklar** = vad är hooken? Kategorisera till en av:
  - Smärta-baserad ("Trött på att...")
  - Outcome-baserad ("Få X på Y veckor")
  - Social proof ("X kunder valde oss")
  - Demo/feature ("Se hur Y funkar")
  - FOMO/urgency ("Begränsat antal")
  - Founder-story ("Jag byggde Y för att...")
- **Format-mix** = ungefärligt: 60% video / 30% statisk / 10% karusell
- **Aktivitets-nivå** = totalt antal aktiva ads. <5 = låg. 5-20 = medel. >20 = aggressiv ads-strategi.

### Anti-scraping fallback

Meta Ads Library är publik men har rate-limiting. Om Firecrawl blockas:

1. Vänta 60 sek, försök igen med samma anrop
2. Om fortsatt blockerat → notera "Meta-data ej tillgänglig denna körning, försök nästa diff"
3. Kringgå INTE rate-limits programmatiskt

### Failure conditions

- Konkurrent har 0 aktiva ads → notera "ingen Meta-ads-närvaro" — det är data
- Konkurrent kör inte i Sverige → ändra country-parameter till "ALL"
- Sökning ger flera advertiser-matches → välj den med Verified-badge eller mest följare. Notera i matrisen vilken du valde.

---

## Sektion 3: LinkedIn-företagsida

### Mål

Anställda-tillväxt, jobbannonser (GTM-roller), top 3 post-ämnen per konkurrent.

### URLs att skrapa

1. LinkedIn-företagsida (`linkedin.com/company/[KONKURRENT]`)
2. Jobs-tabben (`linkedin.com/company/[KONKURRENT]/jobs`)
3. Posts-tabben (`linkedin.com/company/[KONKURRENT]/posts`)

### Firecrawl-anrop

LinkedIn är JS-renderad och har anti-scraping. Använd `firecrawl_extract`:

```
{
  "urls": ["<LINKEDIN_COMPANY_URL>"],
  "schema": {
    "company_name": "string",
    "employee_count": "string — e.g. '11-50 employees'",
    "employee_count_exact": "number — if exact number visible",
    "headquarters": "string",
    "founded": "number — year",
    "specialties": "array of strings"
  }
}
```

För jobs:

```
{
  "urls": ["<LINKEDIN_COMPANY_URL>/jobs"],
  "schema": {
    "open_positions": "array of objects with {title, location, posted_date, function}"
  },
  "prompt": "Extract all open job positions. Focus on identifying function/category (sales, marketing, engineering, customer success, ops)."
}
```

För posts:

```
{
  "urls": ["<LINKEDIN_COMPANY_URL>/posts"],
  "schema": {
    "recent_posts": "array of objects with {post_text_preview, post_date, engagement_count, topic_theme}"
  },
  "prompt": "Extract the 10 most recent posts. For each, capture the first 200 chars of text and identify the topic theme (product launch, thought leadership, hiring, customer story, event, etc.)."
}
```

### Vad du extraherar

- **Anställda-tillväxt** = current count vs senaste matris (om diff-körning). Om första körning: notera bara current count, ingen tillväxt-data.
- **GTM-roller rekryterade** = säljare, account executives, marketing managers, customer success, partnerships. Dessa säger mest om strategi. Engineering-roller säger mindre om GTM.
- **Top 3 post-ämnen** = kategorisera till tema. Vanliga teman:
  - Product launches/updates
  - Thought leadership (CEO/founder posts)
  - Hiring announcements
  - Customer stories
  - Event-närvaro
  - Industry takes / hot tongues

### Anti-scraping fallback

LinkedIn blockar aggressivt. Om Firecrawl returnerar tom data eller login-wall:

1. Försök `firecrawl_extract` med `actions: [{"type": "wait", "milliseconds": 5000}]`
2. Om fortsatt blockerat → använd publik data från konkurrentens egen "About us"-sida på deras hemsida som proxy för anställda-info
3. Använd manuell sökning som backup: be André kolla LinkedIn själv och klistra in nyckeltal

### Failure conditions

- LinkedIn-sida hittas inte → notera "ingen LinkedIn-närvaro" — det är också data (men ovanligt för B2B)
- Login-wall → använd fallback-strategi ovan
- Konkurrent har <50 anställda → tillväxt-data är mindre signifikant, fokusera på jobs-listan istället

---

## Sektion 4: (Valfritt) SEO-data via SEMrush/Ahrefs

Om SEMrush- eller Ahrefs-MCP är tillgänglig — kontrollera först med `mcp__mcp-registry__list_connectors` eller motsvarande.

### Mål

Top 10 organiska sökord + estimerad trafik + top 3 landing pages per konkurrent.

### Generisk prompt

```
{
  "domain": "<konkurrent.se>",
  "country": "se",
  "limit": 10
}
```

### Vad du extraherar

- **Sökord-tema** = kategorisera top 10 till 1-2 teman (t.ex. "AI-kurs", "marknadsföringsautomation", "konsultation")
- **Estimerad organisk trafik** = månadssiffra om tillgänglig
- **Top 3 landing pages** = vilka sidor drar mest trafik. Indikerar var pengarna ligger.

### Failure conditions

- Ingen SEMrush/Ahrefs-MCP tillgänglig → hoppa hela sektionen, flagga som "ej tillgänglig" i matrisen
- Domain har <100 keywords i index → "för liten SEO-närvaro för signal"

---

## Sektion 5: Konsolidering

Efter alla skrapningar — du har en dataset per konkurrent. Innan du bygger matrisen:

1. **Verifiera att varje cell har data eller explicit "ej tillgänglig".** Inga blanka.
2. **Korta ner till matris-friendly format.** Positionering = 1 mening, max 15 ord. Features = stickord, inte fullkomliga meningar.
3. **Quote eller paraphrase, inte tolkning.** "Konkurrent säger 'X för Y'" — inte "Konkurrent verkar positionera sig som...".
4. **Identifiera 3 luckor.** Detta är slutsteget. Inte 1, inte 5. Tre konkreta möjligheter, formulerade som actionable för André.

---

## En notering om rate-limiting

Om du skrapar 5 konkurrenter × 4 källor (hemsida, pricing, Meta, LinkedIn) = 20+ Firecrawl-anrop. Det är inom normal användning men kör inte allt i parallell — kör sekventiellt per konkurrent eller batch om 3 åt gången.

Om Firecrawl returnerar rate-limit-fel:

1. Vänta 30 sek
2. Försök igen
3. Om upprepat fel → pausa körningen, flagga till André, fortsätt senare

Aldrig kringgå rate-limits programmatiskt.
