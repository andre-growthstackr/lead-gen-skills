---
name: analytics-rapport
description: Schemalagd analytics-skill som varje måndag 06:00 frågar 5 MCPs parallellt (GA4, Meta Ads, LinkedIn Ads, HubSpot/HighLevel, Stripe), beräknar CAC + Payback Period + Kanal-bidrag, genererar PowerPoint-rapport med tre paneler, och mejlar till André. CFO-rapport, inga vanity metrics. Använd skillen varje gång användaren skriver "analytics rapport", "veckans rapport", "marketing-rapport", "CFO-rapport", "kör marketing-analytics", eller behöver tre North Star-siffror snarare än tolv KPI:er.
---

# analytics-rapport

Schemalagd skill för veckovis marketing-analytics. Körs via Claude Routine varje måndag 06:00. Output är en PowerPoint som mejlas till André med tre paneler: North Star-siffror, kanal-drill-down, 90-dagars trend.

## Vad skillen gör

- Frågar 5 MCPs parallellt: GA4, Meta Ads, LinkedIn Ads, HubSpot/HighLevel, Stripe
- Beräknar tre North Star-siffror: CAC, Payback Period, Kanal-bidrag
- Genererar `.pptx` med GrowthStackr-branding (tre paneler)
- Mejlar rapporten till André som bilaga
- Schemalagd som Claude Routine varje måndag 06:00

## Vad skillen INTE gör

- Visar inte vanity metrics (CTR, reach, impressions, frequency) i topp-panelen — de hör hemma i drill-down
- Räknar inte sista-klick-attribution som primary — använder HubSpot/HighLevel lead source vid closed/won
- Skapar inte dashboard i Looker/PowerBI — output är .pptx för CFO-läsning
- Hanterar inte kampanj-publicering — det gör `meta-ads-cli` och `linkedin-ads`

## Triggers

Aktivera när användaren skriver:

- "analytics rapport"
- "veckans rapport"
- "marketing-rapport"
- "CFO-rapport"
- "kör marketing-analytics"
- "veckorapport"

Eller automatiskt via Claude Routine varje måndag 06:00.

## Workflow

### Steg 1: Verifiera MCP-anslutningar

Innan första körning, verifiera att alla 5 MCPs är anslutna. Se `references/mcp-setup.md` för auth-steg.

```bash
mcp__ga4__verify_connection
mcp__meta-ads__verify_connection
mcp__linkedin-ads__verify_connection
mcp__hubspot__verify_connection   # eller mcp__highlevel
mcp__stripe__verify_connection
```

Om någon misslyckas — pausa skillen och felsök innan rapport körs.

### Steg 2: Hämta data parallellt

Datafönster: senaste 7 dagar för veckorapport, senaste 90 dagar för trend.

**GA4:**

```
mcp__ga4__run_report
  --dimensions "sessionDefaultChannelGroup"
  --metrics "sessions,conversions,totalRevenue"
  --date-range "last_7d"
```

**Meta Ads:**

```
mcp__meta-ads__metrics_get
  --campaign all
  --date-range last_7d
  --metrics "spend,impressions,clicks,leads,cpa"
```

**LinkedIn Ads:**

```
mcp__linkedin-ads__metrics_get
  --campaign all
  --date-range last_7d
  --metrics "spend,impressions,clicks,leads,cpl"
```

**HubSpot (eller HighLevel):**

```
mcp__hubspot__search_objects
  --object deals
  --filter "dealstage=closedwon AND closedate>=last_7d"
  --properties "amount,hs_analytics_source,closedate,dealstage"
```

**Stripe:**

```
mcp__stripe__list_subscriptions
  --created last_7d
  --status active
```

Kör alla 5 anrop i parallell (single tool-call batch).

### Steg 3: Beräkna tre North Star-siffror

**CAC (Customer Acquisition Cost):**

```
CAC = (Total Spend Meta + Total Spend LinkedIn) / Antal Closed/Won (samma period)
```

Inkludera bara paid spend i numerator — organisk trafik räknas inte som CAC-cost.

**Payback Period:**

```
Payback = CAC / (ARR per kund / 12)
```

ARR per kund hämtas från Stripe (genomsnittlig MRR × 12 för aktiva subscriptions skapade i fönstret).

**Kanal-bidrag:**

```
För varje kanal: % av Closed/Won-pipeline-värde
```

Använd `hs_analytics_source` från HubSpot (eller motsvarande lead source-fält i HighLevel). Sista-klick i GA4 används bara som sekundär kontroll.

### Steg 4: Generera PowerPoint

Använd `growth-os-slides`-skill för att producera 3 slides med GrowthStackr-branding. Template-struktur i `references/pptx-template.md`.

**Slide 1 — Topp-panel (3 North Star-siffror):**

```
+-------------------------------------+
|  CAC          Payback     Kanal-mix |
|  1 870 kr     4,2 mån     Meta 38%  |
|                          LinkedIn 31%|
|                          Organic 21% |
|                          Referral 10%|
+-------------------------------------+
```

**Slide 2 — Drill-down per kanal:**

```
+-------------------------------------+
| Kanal     Spend  Leads  CPL  Pipeline |
| Meta     5.6k   58     97   210k    |
| LinkedIn 4.2k   12     350  165k    |
| Organic   -    23     -    115k    |
| Referral  -    7      -     55k    |
+-------------------------------------+
```

**Slide 3 — 90-dagars trend:**

Line chart med tre lines: CAC, Payback Period, Pipeline-volym. Trendlinje för riktningen.

### Steg 5: Mejla rapporten

```
mcp__gmail__create_draft
  --to "andre@growthstackr.io"
  --subject "Veckorapport vecka [N] — CAC [X kr], Payback [Y mån]"
  --body "Bifogat veckans marketing-rapport. Tre paneler: North Star, drill-down, trend."
  --attachment "./rapport-vecka-N.pptx"
```

Användaren får utkastet i Gmail Drafts — godkänner och skickar manuellt. Skillen mejlar inte automatiskt utan godkännande.

### Steg 6: Schemalägg

Första gången skillen körs:

```
mcp__scheduled-tasks__create_scheduled_task
  --name "analytics-rapport-veckovis"
  --cron "0 6 * * 1"   # Varje måndag 06:00
  --skill analytics-rapport
```

## Output-format

PowerPoint-fil: `rapport-vecka-[N]-[YYYY-MM-DD].pptx`

Slide-struktur:
1. Topp-panel: 3 North Star-siffror (stort)
2. Drill-down per kanal: tabell med spend/leads/CPL/pipeline
3. 90-dagars trend: line chart med CAC, Payback, Pipeline

Plus följebrev i mail-utkastet:

```
Veckorapport vecka [N]

North Star:
- CAC: [X] kr (delta vs förra veckan: [+/-Y%])
- Payback: [X] mån (delta: [+/-Y])
- Topp-kanal: [Kanal] med [X%] av pipeline

Att titta på:
- [1-2 raders observation från drill-down — t.ex. "LinkedIn CPL upp 23% — kolla ad set Smärta-fokus"]
- [Eventuell anomali i 90-dagars trend]

Skickas via analytics-rapport-skill, måndag [datum] 06:00.
```

## Failure conditions

Avbryt och flagga till André om:

- Någon av de 5 MCPs är icke-svarande efter 3 retries
- HubSpot saknar `hs_analytics_source` på >30% av Closed/Won-records (lead source-data är trasigt)
- Stripe-data visar 0 subscriptions skapade på 7 dagar (sanity-check failar)
- CAC blir <0 eller >50 000 kr (sannolikt datafel)
- Antal Closed/Won = 0 — rapport blir meningslös, hoppa över denna vecka och flagga

## References

- `references/mcp-setup.md` — Auth-steg för var och en av de 5 MCPs (GA4, Meta Ads, LinkedIn Ads, HubSpot/HighLevel, Stripe)
- `references/pptx-template.md` — PowerPoint-template-struktur med GrowthStackr-branding (färger, typografi, layout per slide)
