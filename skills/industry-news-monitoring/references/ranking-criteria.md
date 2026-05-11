# Viral-potential-scoring för nyheter

Detta dokument specificerar exakt hur skillen rankar de 20-30 kandidat-nyheterna för att plocka topp-5 till newsletter.

## Scoring-formel

Varje nyhet får ett poäng 0-100, beräknat som:

```
Total = (Delningar × 0.40) + (Källa × 0.25) + (Recency × 0.20) + (Relevans × 0.15)
```

Där varje sub-score är 0-100. Multiplikatorerna vägt totalt = 1.0.

## 1. Delningar / engagement (40%)

Mäts via LinkedIn shares + Twitter shares + kommentarsvolym på artikeln.

| LinkedIn shares | Sub-score |
|---|---|
| > 1 000 | 100 |
| 500-999 | 85 |
| 200-499 | 70 |
| 100-199 | 55 |
| 50-99 | 40 |
| 20-49 | 25 |
| < 20 | 10 |

Twitter shares läggs på som +10 om > 500 retweets.

Om delnings-data saknas (inte alla artiklar har offentlig statistik) — använd proxy:

- LinkedIn-sökning på artikel-URL → antal posts som länkar
- Twitter-sökning på artikel-URL → antal tweets

**Notering:** Detta är där skillen är mest känslig — om Firecrawl inte kommer åt delnings-data, blir scoringen baserad på proxies. Var transparent i ranking-tabellen om source-datat saknades.

## 2. Källans auktoritet (25%)

Hardcoded tier-lista (uppdaterbar av André):

| Källa | Tier | Sub-score |
|---|---|---|
| WSJ, NYT, FT, The Information | S | 100 |
| TechCrunch, The Verge, Wired, Bloomberg | A | 90 |
| MarTech Today, Marketing Brew, Stratechery | A | 90 |
| Anthropic blog, OpenAI blog, Google AI blog | A | 90 |
| Andra etablerade tech-publikationer | B | 75 |
| Etablerad blogg (Tomasz Tunguz, Lenny's, etc) | B | 75 |
| Företagsblogg (SaaS-bolag) | C | 60 |
| Medium-blogg från känd person | C | 60 |
| Sub-stack från okänd | D | 40 |
| Random blogg / forum-tråd | E | 25 |

Lista underhålls i `icp-spec.md` under sektionen "Källor".

## 3. Recency (20%)

| Publiceringsdatum | Sub-score |
|---|---|
| < 24h sedan | 100 |
| 24-48h | 85 |
| 48-72h | 70 |
| 72h - 5 dagar | 55 |
| 5-7 dagar | 35 |
| > 7 dagar | 15 |

Newsletter går ut fredag — så nyheter från måndag-torsdag samma vecka är default-fönstret. Fredagsnyheter samma morgon är guldläge.

## 4. Relevans för ICP (15%)

Match mot ICP-sökord från `icp-spec.md`. Räkna:

- Antal sökord-matches i titeln (vikt × 3)
- Antal sökord-matches i sammanfattning (vikt × 1)

Normalisera till 0-100 baserat på max möjlig match.

**Exempel:** ICP-sökord = ["B2B", "marketing", "AI", "Claude", "pipeline", "content"]

- Artikel-titel: "Hur Claude transformerar B2B-marketing-pipelines" → 4 matches × 3 = 12
- Artikel-sammanfattning: nämner "AI" + "content" → 2 matches × 1 = 2
- Total: 14 / max 18 = 78

## Exempel-beräkning

Artikel: "OpenAI släpper enterprise agent-mode med Claude-konkurrens"
Källa: The Information
Datum: 36h sedan
LinkedIn shares: 1 240
Twitter retweets: 680
Sökord-matches: 3 i titel, 2 i sammanfattning

| Faktor | Sub-score | Vikt | Bidrag |
|---|---|---|---|
| Delningar | 100 + 10 (twitter bonus) = 110, cap 100 | 0.40 | 40 |
| Källa | 100 (S-tier) | 0.25 | 25 |
| Recency | 85 (24-48h) | 0.20 | 17 |
| Relevans | 11/18 = 61 | 0.15 | 9.15 |
| **Total** | | | **91.15** |

Detta är en topp-3-nyhet utan tvekan.

## Edge-cases

### Nyhet utan delnings-data

Sätt sub-score 40 (mitt på) och flagga i ranking-tabellen: "delnings-data saknas, proxy-rating använd".

### Nyhet från ICP-favorit-källa (ej tier-listad)

Lägg till källan i tier-listan på rätt nivå. André kan korrigera.

### Två nyheter om samma händelse

Plocka den högst-rankade. Diversifiera annars — inte två AI-agent-nyheter samma vecka.

### Nyhet från sponsrad placering

Behandla som tier-D (40), oavsett vart den publicerades. Sponsrade nyheter får inte plats i topp-5.

## Manual override

Skillen levererar ranking-tabellen som första output. André kan välja att override:a en nyhet manuellt — t ex om något var stort på LinkedIn-feeds som inte fångades upp. Skillen accepterar override och regenererar outline.

## Diversifierings-regel

Topp-5 får INTE bestå av:

- Mer än 2 nyheter från samma källa
- Mer än 2 nyheter om samma vertikal (alla AI / alla SaaS / alla agencies)
- Mer än 1 nyhet om samma företag

Om topp-5 bryter mot detta — droppa lägsta inom överlappande kategori och plocka nästa från listan.
