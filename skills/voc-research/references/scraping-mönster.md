# Scraping-mönster för VoC-källor

Konkreta Firecrawl-mönster per källa, plus workarounds när siter blockerar.

## G2.com

**URL-mönster för 1-2 stjärniga reviews:**
```
https://www.g2.com/products/[product-slug]/reviews?filters%5Bnps_score%5D%5B%5D=1&filters%5Bnps_score%5D%5B%5D=2
```

**Firecrawl-anrop:**
```
mcp__firecrawl__firecrawl_scrape
url: [URL ovan]
formats: ["markdown"]
onlyMainContent: true
```

**Vanlig paginering:** G2 visar ~25 reviews/sida. Iterera med `?page=2`, `?page=3` etc.

**Om G2 blockerar:** Firecrawl har ofta rätt headers. Om det ändå failas, prova:
- `mcp__firecrawl__firecrawl_extract` med schema `{rating, body, date, reviewer_role}`
- Eller fallback till en cached version via Google search

## Capterra

**URL:**
```
https://www.capterra.com/p/[id]/[name]/reviews/
```

**Filter på låg-rating:** Capterras filter funkar inte alltid via URL — scrapa allt och filtrera i efterhand på "Cons"-sektionen.

**Capterra-specifikt fält:** Reviews har "Pros" och "Cons" som separata sektioner. Cons är guld för VoC.

## Trustpilot

**URL:**
```
https://www.trustpilot.com/review/[domain]?stars=1&stars=2
```

**Firecrawl-anrop:** Standard scrape. Trustpilot har bra strukturerad HTML.

## Reddit

**Discovery:**
```
mcp__firecrawl__firecrawl_search
query: "[konkurrent] reddit"
limit: 20
```

**Tråd-scraping:**
```
mcp__firecrawl__firecrawl_scrape
url: [reddit-url].json
formats: ["markdown"]
```

**Tips:** Lägg `.json` på slutet av en Reddit-URL för att få JSON istället för rendered HTML — mycket renare för parsing.

**Sub-specifika sökningar (via Google):**
```
site:reddit.com/r/SaaS "[konkurrent]" frustrating
site:reddit.com/r/marketing "switched from [konkurrent]"
```

## YouTube

**Kommentar-scraping är jobbigt.** YouTube blockerar mycket. Alternativ:

- **Firecrawl + youtube watch-URL:** Funkar för videosbeskrivning och toppkommentarer, inte alla.
- **Manuell paste:** Be användaren öppna videon, scrolla genom kommentarerna och paste:a topp-50 till dig.
- **Tredjepart:** Verktyg som SocialBlade eller VidIQ exporterar kommentarer, men kräver inlogg.

**Sök efter rätt video:**
```
mcp__firecrawl__firecrawl_search
query: "[konkurrent] honest review youtube"
```

## När en sajt blockerar

1. **Vänta + retry.** Ibland är det rate limit.
2. **Använd `mcp__firecrawl__firecrawl_extract` med schema** — Extraherar oftare än rå scrape.
3. **Fallback till Google cache** — Sök `cache:[url]`.
4. **Sist:** Be användaren paste:a in själv. Att fejka är värre än att vara transparent.

## Rensning av scrape-output

Firecrawl ger ofta markdown med:
- Navigationselement
- Footer-links
- Ad-text
- Cookie-banner-text

Filtrera bort innan klustering. Mönster att leta efter:
- "Sign up", "Get started", "Log in" — navigation
- "© 2024" — footer
- "Use cookies", "Privacy preferences" — banner
- "Sponsored", "Ad" — annonser

Behåll bara: review body, rating, datum, reviewer-role om tillgängligt.

## Volymkontroll under scrapingen

Räkna datapunkter löpande. När du når ~50% av målet (50 av 100) — pausa, kvalitetscheckta råmaterialet, fortsätt. Det här fångar fel-scrapes innan du har 100 dåliga datapunkter.

Logga datapunkter per källa i en enkel tabell:

| Källa | URL | Datapunkter |
|---|---|---|
| G2 (konkurrent A) | [url] | 32 |
| Capterra (konkurrent A) | [url] | 18 |
| Reddit r/SaaS | [trådar] | 24 |
| YouTube | [videos] | 26 |
| **Totalt** | | **100** |
