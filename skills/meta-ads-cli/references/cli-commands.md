# Meta Ads CLI — cheatsheet

Vanligaste kommandon för Meta Ads CLI. Officiell dokumentation: https://developers.facebook.com/docs/marketing-api/cli

---

## Auth

```bash
# Logga in (öppnar OAuth-flöde i webbläsare)
meta-ads auth login

# Verifiera autentisering
meta-ads auth status

# Lista tillgängliga ad accounts
meta-ads accounts list

# Sätt default account för session
meta-ads config set account act_<ID>
```

---

## Audiences

```bash
# Skapa Custom Audience från CSV
meta-ads audiences create \
  --name "ClosedWon-YYYYMMDD" \
  --type CUSTOM \
  --source-csv ./customers.csv \
  --hash-fields email,phone,first_name,last_name \
  --account act_<ID>

# Skapa Lookalike från Custom Audience
meta-ads audiences lookalike \
  --source <CUSTOM_AUDIENCE_ID> \
  --country SE \
  --ratio 0.01

# Lista audiences
meta-ads audiences list --account act_<ID>

# Status på audience (Meta behöver tid för att matcha)
meta-ads audiences status <AUDIENCE_ID>
```

CSV-format: `email,phone,fn,ln,country,city,state,zip`. Alla PII SHA256-hashas av CLI om `--hash-fields` är satt.

---

## Campaigns

```bash
# Skapa kampanj
meta-ads campaigns create \
  --name "LeadGen-Q1-2026" \
  --objective LEAD_GENERATION \
  --status PAUSED \
  --account act_<ID>

# Aktivera
meta-ads campaigns update <CAMPAIGN_ID> --status ACTIVE

# Lista
meta-ads campaigns list --account act_<ID>

# Pausa
meta-ads campaigns update <CAMPAIGN_ID> --status PAUSED
```

Objectives: `LEAD_GENERATION`, `CONVERSIONS`, `ENGAGEMENT`, `TRAFFIC`, `BRAND_AWARENESS`.

---

## Ad sets

```bash
# Skapa ad set
meta-ads adsets create \
  --campaign <CAMPAIGN_ID> \
  --name "AS-Smarta-Fokus" \
  --daily-budget 20000 \
  --audience <AUDIENCE_ID> \
  --optimization LEAD \
  --billing IMPRESSIONS \
  --placement automatic

# Ändra budget
meta-ads adsets update <ADSET_ID> --daily-budget 25000

# Lägg till lookalike
meta-ads adsets update <ADSET_ID> --add-audience <LOOKALIKE_ID>
```

Budget anges i öre. 20000 = 200 kr/dag.

---

## Creatives

```bash
# Ladda upp video-creative
meta-ads creatives upload \
  --adset <ADSET_ID> \
  --media ./video-smarta-v1.mp4 \
  --headline "Tre månader. Noll deals." \
  --body "Du har testat fler verktyg. Det är inte verktyget. Boka 30 min för att förstå varför." \
  --cta "LEARN_MORE" \
  --landing-page https://growthstackr.io/boka

# Ladda upp image-creative
meta-ads creatives upload \
  --adset <ADSET_ID> \
  --media ./image-smarta-v1.jpg \
  --headline "..." \
  --body "..." \
  --cta "..."

# Lista creatives per ad set
meta-ads creatives list --adset <ADSET_ID>

# Pausa en specifik creative
meta-ads creatives update <CREATIVE_ID> --status PAUSED
```

CTA-alternativ: `LEARN_MORE`, `SIGN_UP`, `DOWNLOAD`, `BOOK_NOW`, `GET_QUOTE`, `CONTACT_US`.

---

## Metrics

```bash
# Hämta metrics per kampanj
meta-ads metrics get \
  --campaign <CAMPAIGN_ID> \
  --date-range yesterday \
  --metrics spend,impressions,clicks,leads,cpa,ctr

# Drill-down per ad set
meta-ads metrics get \
  --adset <ADSET_ID> \
  --date-range last_7d \
  --breakdown creative

# Export till JSON för analytics-skill
meta-ads metrics get \
  --campaign <CAMPAIGN_ID> \
  --date-range last_30d \
  --output json > metrics.json
```

Date-range-alternativ: `today`, `yesterday`, `last_7d`, `last_14d`, `last_30d`, `last_90d`, eller `YYYY-MM-DD,YYYY-MM-DD`.

---

## Scheduling (för Auto-Research-loop)

```bash
# Hämta gårdagens CPA för jämförelse
YESTERDAY_CPA=$(meta-ads metrics get \
  --campaign <CAMPAIGN_ID> \
  --date-range yesterday \
  --metrics cpa \
  --output value)

DAY_BEFORE_CPA=$(meta-ads metrics get \
  --campaign <CAMPAIGN_ID> \
  --date-range -2d \
  --metrics cpa \
  --output value)

# Beräkna stegring i bash
if (( $(echo "$YESTERDAY_CPA > $DAY_BEFORE_CPA * 1.2" | bc -l) )); then
  # Trigga ny VoC + creatives
  claude run-skill voc-research
  claude run-skill ad-ideation-4-angles
fi
```

Schemalägg hela skriptet via `mcp__scheduled-tasks__create_scheduled_task` med cron `0 7 * * *`.

---

## Felsökning

| Fel | Orsak | Fix |
|---|---|---|
| `401 Unauthorized` | Token utgången | Kör `meta-ads auth login` igen |
| `Audience too small` | <100 matchade records | Lägg till fler Closed/Won eller bredda källan |
| `Creative rejected` | Bryter Meta-policy | Kolla Ads Manager för specifik orsak, justera copy |
| `Budget too low` | Under Metas minimum för objective | Öka till minst 50 kr/dag |
