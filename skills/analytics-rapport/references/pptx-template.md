# PPTX-template för veckorapport

PowerPoint-template-struktur med GrowthStackr-branding. Använd `growth-os-slides`-skill för faktisk rendering.

---

## Brand-element (GrowthStackr)

**Färger:**
- Bakgrund (canvas): `#0E0E0E` (near-black)
- Primär text: `#F5F1EA` (cream)
- Accent: `#FF6B47` (coral)
- Datavisualisering: coral primary, cream + dimmad coral secondary

**Typografi:**
- Headlines: Instrument Serif italic (för rubriker och stora siffror)
- Body + tabeller: Inter Regular
- Stora siffror på topp-panel: Instrument Serif italic, 96-128pt

**Layout-grid:**
- 16:9 (1920×1080)
- 80px margins
- Wordmark "GrowthStackr" bottom-right på varje slide (Inter Medium, 18pt, cream)
- Datum + vecka top-right (Inter Regular, 14pt)

---

## Slide 1 — Topp-panel (North Star)

**Syfte:** Tre siffror som CFO bryr sig om. Stort. Inget annat.

### Layout

```
+------------------------------------------------------+
|  [datum] vecka [N]                  GrowthStackr     |
|                                                      |
|   CAC          Payback           Kanal-bidrag        |
|                                                      |
|   1 870 kr     4,2 mån           Meta      38%       |
|   ↑ 12%        ↓ 0,3 mån         LinkedIn  31%       |
|   vs förra v   vs förra v        Organic   21%       |
|                                  Referral  10%       |
|                                                      |
|                                                      |
+------------------------------------------------------+
```

### Element

- 3 kolumner, lika breda
- Stort siffervärde överst per kolumn (Instrument Serif italic, 96pt)
- Delta-pil + procent under varje siffra (Inter, 18pt, coral om upp, cream om ner)
- Kanal-bidrag är top 4 kanaler med procent

### Regler

- Aldrig fler än 3 North Star-siffror — om en fjärde behövs är två redan inte North Star
- Delta-färg: coral för riktning som är **bra** (lägre CAC, kortare payback), inte alltid uppåt
- Inga decimaler på CAC (avrunda till heltal kr)
- Payback i månader, 1 decimal

---

## Slide 2 — Drill-down per kanal

**Syfte:** Förstå VARFÖR North Star-siffrorna ser ut som de gör.

### Layout

```
+------------------------------------------------------+
|  Drill-down per kanal              vecka [N]         |
|                                                      |
|  Kanal     Spend    Leads   CPL     Pipeline   ROAS  |
|  -------------------------------------------------   |
|  Meta      5 600    58      97      210 000    3.5x  |
|  LinkedIn  4 200    12      350     165 000    3.9x  |
|  Organic     -      23      -       115 000    -     |
|  Referral    -      7       -        55 000    -     |
|                                                      |
|  Total:    9 800    100     -       545 000    -     |
|                                                      |
|  Anmärkningar:                                       |
|  • LinkedIn CPL upp 23% — ad set Smärta-fokus        |
|  • Organic-pipeline från SEO-content L4.5            |
|                                                      |
+------------------------------------------------------+
```

### Element

- Tabell med 5-6 kolumner: Kanal / Spend / Leads / CPL / Pipeline / ROAS
- Sortering: spend desc (Meta först om mest spend)
- Total-rad längst ner med relevanta summor
- Anmärkningar-sektion: 1-3 bullets från LLM-analys av data

### Regler

- "-" istället för "N/A" för kanaler utan spend
- ROAS bara om pipeline + spend finns för kanalen
- Anmärkningar ska vara handlings-orienterade ("kolla X", "öka Y"), inte beskrivande

---

## Slide 3 — 90-dagars trend

**Syfte:** Se riktning, inte enskild punkt.

### Layout

```
+------------------------------------------------------+
|  90-dagars trend                                     |
|                                                      |
|         CAC               Payback          Pipeline  |
|       ___                ___              ___        |
|      /   \              /   \            /   \       |
|     / 1.8k\            / 4.2 \          /545k \      |
|     -------            -------          --------     |
|                                                      |
|  [Line chart över 13 veckor]                         |
|                                                      |
|     CAC ──── coral                                   |
|     Payback ──── cream                               |
|     Pipeline ──── dimmad coral                       |
|                                                      |
|  Trendlinje (12-veckors moving average):             |
|  CAC: ↓ 8% trend — bra riktning                      |
|  Payback: ↓ 0,4 mån trend — bra riktning             |
|  Pipeline: ↑ 18% trend — bra riktning                |
|                                                      |
+------------------------------------------------------+
```

### Element

- 3 mini-cards överst med senaste värdet per siffra
- Line chart med 13 datapunkter (en per vecka, senaste 13 veckorna)
- 3 lines: CAC, Payback, Pipeline — separata Y-axlar
- Trendlinje-sammanfattning under chart (12-veckors moving average riktning)

### Regler

- Aldrig fler än 3 lines i charten — fler blir spaghetti
- Y-axlar normaliserade så ingen line dominerar visuellt
- Trendlinje-text: alltid "bra riktning" eller "att titta på" — aldrig neutralt

---

## Anti-patterns att undvika

- ❌ Inkludera CTR, reach, impressions i topp-panel (vanity metrics — hör hemma i drill-down)
- ❌ Visa sista-klick attribution som primary (använd HubSpot lead source vid closed/won)
- ❌ Mer än 3 lines i trend-charten
- ❌ Pie-chart för kanal-bidrag (tabell är bättre)
- ❌ Emoji i text (det är CFO-rapport)
- ❌ Stora numeriska siffror utan delta — siffran säger inget utan kontext

---

## Filnamnskonvention

`rapport-vecka-[N]-[YYYY-MM-DD].pptx`

Exempel: `rapport-vecka-19-2026-05-11.pptx`

Sparas i `~/Documents/GrowthStackr/Veckorapporter/`.
