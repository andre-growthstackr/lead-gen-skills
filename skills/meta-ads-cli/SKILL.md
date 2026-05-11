---
name: meta-ads-cli
description: Publicera och hantera Meta Ads-kampanjer programmatiskt via Meta Ads CLI direkt från Claude Code/terminalen. Installation, autentisering, Custom Audience-upload från CRM, kampanjstruktur med 4 ad sets (en per vinkel från ad-ideation-4-angles), 12 creative-varianter, samt Auto-Research-loop som triggas vid CPA-stegring >20%. Använd skillen varje gång användaren skriver "Meta Ads CLI", "publicera Meta-kampanj", "Meta Ads programmatiskt", "kör Meta-annonsering", eller vill driva Meta-kampanjer från terminal istället för Ads Manager UI.
---

# meta-ads-cli

Skill för att köra Meta Ads-kampanjer programmatiskt från Claude Code. Bygger på Meta Marketing API och Meta Ads CLI — samma maskineri som Ads Manager UI, fast skriptbart.

## Vad skillen gör

- Installerar och autentiserar Meta Ads CLI mot Business Manager
- Laddar upp Custom Audience från CRM (HubSpot/HighLevel via MCP) till Meta
- Skapar Lead Generation-kampanj med 4 ad sets (en per vinkel från `ad-ideation-4-angles`)
- Lägger till 4 videos + 12 image-ads från Airtable Content OS som varianter — låter Andromeda optimera
- Sätter dagsbudget 200 kr/dag per ad set
- Konfigurerar Auto-Research-loop som triggas vid CPA-stegring >20%

## Vad skillen INTE gör

- Producerar inte creatives — det gör L4.2 (video) och L4.3 (image)
- Skriver inte vinklar eller hooks — det gör `ad-ideation-4-angles`
- Hanterar inte LinkedIn — det gör `linkedin-ads-coldiq-wrapper`
- Bygger inte analytics-rapporter — det gör `analytics-rapport`

## Triggers

Aktivera när användaren skriver:

- "Meta Ads CLI"
- "publicera Meta-kampanj"
- "Meta Ads programmatiskt"
- "kör Meta-annonsering"
- "lansera Meta-kampanj från terminal"
- "Meta lead gen-kampanj"
- "Andromeda-optimerad kampanj"

## Workflow

### Steg 1: Installation och autentisering

```bash
npm install -g @meta-ads/cli
meta-ads auth login
```

Användaren får OAuth-flöde till Meta Business Manager. Användaren godkänner manuellt i webbläsare — Claude initierar inte autentiseringen själv.

Verifiera med:

```bash
meta-ads accounts list
```

Spara `act_<ID>` för konton som ska användas.

### Steg 2: Custom Audience-upload

Exportera Closed/Won från CRM via HubSpot MCP (eller HighLevel motsvarande):

1. Anropa `mcp__hubspot__search_objects` med filter `dealstage = closedwon`
2. Hämta email + telefon per deal
3. Spara som `.csv` med Meta:s required-format (email, phone, fn, ln, country)
4. Hash:a personuppgifter med SHA256 innan upload (Meta-krav)

Ladda upp:

```bash
meta-ads audiences create \
  --name "ClosedWon-Customers-YYYYMMDD" \
  --type CUSTOM \
  --source-csv ./closedwon.csv \
  --account act_<ID>
```

Lookalike skapas automatiskt:

```bash
meta-ads audiences lookalike \
  --source <CUSTOM_AUDIENCE_ID> \
  --country SE \
  --ratio 0.01
```

### Steg 3: Kampanjstruktur

En Lead Generation-kampanj med 4 ad sets — en per vinkel från `ad-ideation-4-angles`.

```bash
# Kampanj
meta-ads campaigns create \
  --name "LeadGen-Q[N]-YYYY" \
  --objective LEAD_GENERATION \
  --account act_<ID>

# Ad set per vinkel (×4)
meta-ads adsets create \
  --campaign <CAMPAIGN_ID> \
  --name "AS-Smarta-Fokus" \
  --daily-budget 20000 \
  --audience <LOOKALIKE_ID> \
  --optimization LEAD
```

Dagsbudget i öre — `20000` = 200 kr/dag.

Cheatsheet för fler kommandon: läs `references/cli-commands.md`.

### Steg 4: Creatives till ad sets

Hämta från Airtable Content OS-tabell (status = "Approved for Ads"):

- 4 videos (en per vinkel, från L4.2)
- 12 image-ads (3 per vinkel, från L4.3)

Pusha till respektive ad set:

```bash
meta-ads creatives upload \
  --adset <ADSET_ID> \
  --media ./video-smarta-v1.mp4 \
  --headline "[Hook från ad-ideation-4-angles]" \
  --body "[Body från ad-ideation-4-angles]" \
  --cta "BOOK_TRAVEL_NOW"
```

Per vinkel: 1 video + 3 images = 4 varianter. Total 16 creatives över 4 ad sets. Andromeda får data att optimera mot.

### Steg 5: Auto-Research-loop

Sätt upp scheduled task som körs varje morgon kl 07:00:

```bash
meta-ads metrics get \
  --campaign <CAMPAIGN_ID> \
  --date-range yesterday \
  --metrics cpa,spend,leads
```

Om `cpa_yesterday > cpa_day_before_yesterday * 1.20`:

1. Kör `voc-research`-skill för ny VoC-scrape
2. Kör `ad-ideation-4-angles` för 4 nya hooks
3. Producera 4 nya creative-varianter via L4.2/L4.3
4. Pusha som nya varianter till befintliga ad sets — pausa inte gamla, låt Andromeda välja

Schemalägg via `mcp__scheduled-tasks__create_scheduled_task`.

## Output-format

Efter lyckad kampanjlansering, leverera till användaren:

```markdown
# Meta-kampanj lanserad — [datum]

**Kampanj-ID:** <CAMPAIGN_ID>
**Total dagsbudget:** 800 kr/dag (4 ad sets × 200 kr)
**Custom Audience:** ClosedWon-Customers-YYYYMMDD (X seed-records)
**Lookalike:** 1% Sverige

## Ad sets

| Vinkel | Ad set-ID | Creatives | Budget |
|---|---|---|---|
| Smärta-fokus | <ID> | 1 video + 3 image | 200 kr/dag |
| Lösnings-fokus | <ID> | 1 video + 3 image | 200 kr/dag |
| Social proof | <ID> | 1 video + 3 image | 200 kr/dag |
| ROI/data | <ID> | 1 video + 3 image | 200 kr/dag |

## Auto-Research-loop

- Scheduled task: dagligen 07:00
- Trigger: CPA-stegring >20%
- Action: ny VoC + 4 nya hooks + 4 nya creatives

## Nästa rapport

Måndag 06:00 via `analytics-rapport`-skill.
```

## Failure conditions

Avbryt och fråga användaren om:

- Meta Business Manager-autentisering inte är gjord (kräver manuell OAuth)
- CRM saknar minst 50 Closed/Won-records för Custom Audience (för litet seed)
- Creatives saknar status "Approved for Ads" i Airtable
- Användaren ber om budget >2 000 kr/dag per ad set utan godkännande (eskalation)
- Användaren ber om att stänga av Auto-Research-loop — fråga varför, eftersom den är canon för Modul 6

## References

- `references/cli-commands.md` — Cheatsheet för vanligaste Meta Ads CLI-kommandon (campaigns, adsets, creatives, audiences, metrics, scheduling)
