# TAM / SAM — räkneexempel och datakällor

Konkret hur du räknar TAM/SAM för en svensk/nordisk B2B-ICP. Använd siffror, inte gut feel.

## Svenska datakällor

- **allabolag.se** — Filtrera på bransch (SNI-kod), antal anställda, omsättning. Gratis för basic search.
- **SCB (scb.se)** — Företagsregistret. Aggregerad data per SNI + storlek.
- **Crunchbase** — För SaaS/tech, hitta funding-rundor och anställda-spann.
- **LinkedIn Sales Navigator** — Filtrera på industry + headcount + location. Bra för verifiering mot beslutsfattare-roller.
- **Apollo / Cognism / Vainu** — Combined firmographics + tech stack + intent. Vainu är starkast på Norden.

## Räkne-mall

### Steg 1: TAM (Total Addressable Market)

Antal företag i geografin som matchar firmographics-filtret. Ignorera tech stack och köpfönster här — bara grund-fitten.

Exempel:
- Filter: SaaS-bolag i Sverige, 20-200 anställda
- allabolag-sökning på SNI 62.01 (programutveckling) + 20-199 anställda → 412 företag
- LinkedIn-verifiering: 380 företag har minst en CMO/Head of Marketing-roll
- **TAM = 380 företag**

### Steg 2: SAM (Serviceable Addressable Market)

Subset av TAM som även matchar tech stack + visat någon intent-signal senaste 12 mån.

- Antagande: ~25% av TAM har bytt eller är på väg att byta marketing automation
- Antagande: ~40% av TAM har anställt growth/marketing-roll senaste 12 mån
- Overlap-uppskattning: ~15-20% matchar både
- **SAM = ~60-75 företag**

Konservativt — ta nedre delen av spannet om du är osäker.

### Steg 3: Dagligt lead-mål

Givet:
- ARR-mål: 1,2 MSEK
- ACV: 120 000 kr/år
- Antal deals/år: 10
- Genomsnittlig win rate på qualified leads: 20%
- Pipeline coverage: 3x (industri-standard för B2B SaaS)

Räkning:
- Deals/år: 10
- Qualified leads/år: 10 / 20% = 50
- Pipeline leads/år: 50 × 3 = 150
- Pipeline leads/månad: 12,5
- **Pipeline leads/arbetsdag: ~0,6**

På 220 arbetsdagar = 150 / 220 = 0,68. Avrunda till 1 lead/dag som mål.

## Vanliga fel

- **Räkna LinkedIn-followers som TAM.** Followers ≠ companies.
- **Antaga 100% är reachable.** Räkna 50-70% reachable beroende på data-kvalitet.
- **Ignorera säljcykeln.** På 6-mån säljcykel måste pipelinen byggas 6 mån i förväg. Justera daily mål uppåt under bygg-fasen.
- **Glömma uppsälj.** Om expansion ARR är stor del av planen — räkna inte alla nya deals från cold pipeline.

## När TAM ser för stor ut

Om TAM > 5 000 företag — ICP:n är för bred. Snäva in på:
- Specifik bransch (inte "alla SaaS" utan "vertikal-SaaS för bygg")
- Specifik fas (inte "alla 20-200" utan "Series A med >50 anställda")
- Specifik trigger (inte "alla CMOs" utan "CMOs som tillträtt senaste 6 mån")

En användbar ICP har TAM 200-2 000 företag. Större än så och du säljer brett. Mindre och du har en nisch som kan kräva annan go-to-market.
