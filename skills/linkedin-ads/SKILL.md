---
name: linkedin-ads
description: Publicera och hantera LinkedIn Ads programmatiskt via LinkedIn Marketing API direkt från Claude Code. Autentiserar mot LinkedIn Campaign Manager, laddar upp Custom Audience (Closed/Won + Signal-Scraping-profiler), skapar Lead Gen Forms-kampanj med 4 ad sets per vinkel, konfigurerar Lookalike och sätter cap. Använd skillen varje gång användaren skriver "LinkedIn Ads", "LinkedIn Lead Gen Forms", "LinkedIn-kampanj", eller vill köra LinkedIn-annonsering programmatiskt utan att klicka i Campaign Manager UI:t.
---

# linkedin-ads

LinkedIn Ads-publicering programmatiskt — kursens kampanjstruktur, vinkel-mappning och cap-regler ovanpå LinkedIn Marketing API. När den körts klart är 4 ad sets (en per vinkel från `ad-ideation-4-angles`) live med Lead Gen Forms, Custom Audience + Lookalike och CPL-cap som skyddar budget.

## Vad skillen gör

- Autentiserar mot LinkedIn Campaign Manager via OAuth + verifierar Insight Tag från Modul 3
- Laddar upp Custom Audience: Closed/Won från CRM + 2 000+ profil-URLs från Signal-Scraping
- Skapar Lead Gen Forms-kampanj: 4 ad sets (en per vinkel från `ad-ideation-4-angles`)
- Lägger till Single Image + Video-ads från Airtable Content OS
- Konfigurerar Lookalike på Closed/Won som expansion-audience
- Sätter cap: 200 kr/dag per ad set, paus om CPL >3 000 kr efter 100 klicks

## Vad skillen INTE gör

- Producerar inte creatives — L4.2 (video) och L4.3 (image) gör det
- Skriver inte vinklar — `ad-ideation-4-angles` gör det
- Hanterar inte Meta — `meta-ads-cli` gör det
- Hanterar inte rapportering — `analytics-rapport` gör det

## Triggers

Aktivera när användaren skriver:

- "LinkedIn Ads"
- "LinkedIn Lead Gen Forms"
- "LinkedIn-kampanj"
- "LinkedIn programmatiskt"
- "kör LinkedIn-annonsering"
- "publicera LinkedIn-annons"

## Underliggande implementation

Skillen anropar **LinkedIn Marketing API** direkt (REST). Endpoints och dokumentation:

- LinkedIn Marketing API: https://learn.microsoft.com/en-us/linkedin/marketing/
- Lead Gen Forms: https://business.linkedin.com/marketing-solutions/native-advertising/lead-gen-ads
- Matched Audiences: https://business.linkedin.com/marketing-solutions/ad-targeting/matched-audiences
- Insight Tag: https://www.linkedin.com/help/lms/answer/a427660

Två integrations-vägar beroende på vad användaren har redan installerat:

1. **LinkedIn Marketing API direkt** (rekommenderat) — Skillen anropar REST-endpoints via `curl` eller en lättviktig Python-klient. Tokens sparas i `~/.claude/credentials/linkedin.json`.
2. **Community LinkedIn-MCP** — Om användaren har en LinkedIn MCP-server installerad anropar Skillen den istället.

## Workflow

### Steg 1: Autentisera mot LinkedIn Campaign Manager

OAuth-flow med scopes: `r_ads`, `rw_ads`, `r_ads_reporting`, `w_member_social`.

```bash
# Öppna OAuth-URL i webbläsare
open "https://www.linkedin.com/oauth/v2/authorization?response_type=code&client_id=$LINKEDIN_CLIENT_ID&redirect_uri=$REDIRECT_URI&scope=r_ads%20rw_ads%20r_ads_reporting"

# Användaren godkänner manuellt, callback ger en code
# Exchange code → access_token
curl -X POST https://www.linkedin.com/oauth/v2/accessToken \
  -d "grant_type=authorization_code&code=$CODE&client_id=$LINKEDIN_CLIENT_ID&client_secret=$LINKEDIN_CLIENT_SECRET&redirect_uri=$REDIRECT_URI"
```

Spara token i `~/.claude/credentials/linkedin.json`.

Verifiera Insight Tag (från Modul 3 L3.2) genom att besöka Campaign Manager → Account Assets → Insight Tag. Status ska vara "Active".

### Steg 2: Custom Audience-upload

Två källor:

**Källa A — Closed/Won från CRM:**

```bash
# Export via HubSpot MCP
mcp__hubspot__search_objects --filter "dealstage=closedwon" \
  --properties "email,linkedin_url" > closedwon.csv
```

**Källa B — Signal-Scraping-profiler:**

Från Modul 5:s `list-building` + `lead-enrichment-scoring` — engagers på din nischs LinkedIn-content. 2 000+ profil-URLs i `signal-profiles.csv`.

Kombinera och pusha till LinkedIn Matched Audiences:

```bash
curl -X POST "https://api.linkedin.com/rest/dmpSegments" \
  -H "Authorization: Bearer $TOKEN" \
  -H "LinkedIn-Version: 202405" \
  -H "X-Restli-Protocol-Version: 2.0.0" \
  -d @audience-payload.json
```

LinkedIn behöver 48 timmar för att matcha profiler — vänta innan kampanjlansering.

### Steg 3: Skapa Lead Gen Form (engångs-setup)

```bash
curl -X POST "https://api.linkedin.com/rest/leadGenForms" \
  -H "Authorization: Bearer $TOKEN" \
  -H "LinkedIn-Version: 202405" \
  -d '{
    "name": "B2B-Marketer-LeadGen-Form-2026",
    "questions": ["FIRST_NAME","LAST_NAME","EMAIL","COMPANY","JOB_TITLE"],
    "thankYouMessage": "Tack! Vi hör av oss inom 24 timmar.",
    "thankYouUrl": "https://growthstackr.io/tack"
  }'
```

### Steg 4: Skapa kampanj + 4 ad sets

Funnel-arkitekturen (3-layer cold/warm/hot) beskrivs i `references/funnel-architecture.md`. Läs den innan första kampanjlansering.

```bash
# 1. Skapa kampanjgrupp (Campaign)
curl -X POST "https://api.linkedin.com/rest/adAccounts/$ACCOUNT/campaigns" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{
    "name": "LeadGen-LinkedIn-Q2-2026",
    "objectiveType": "LEAD_GENERATION",
    "type": "SPONSORED_UPDATES",
    "status": "PAUSED"
  }'

# 2. Skapa 4 ad sets (Creative groups) — en per vinkel
for VINKEL in smarta losning social-proof roi; do
  curl -X POST "https://api.linkedin.com/rest/adAccounts/$ACCOUNT/campaigns" \
    -H "Authorization: Bearer $TOKEN" \
    -d "{
      \"name\": \"AS-$VINKEL\",
      \"campaignGroup\": \"urn:li:sponsoredCampaignGroup:$GROUP_ID\",
      \"dailyBudget\": {\"amount\": \"200\", \"currencyCode\": \"SEK\"},
      \"targetingCriteria\": $TARGETING_JSON
    }"
done
```

### Steg 5: Lägg till creatives

Från Airtable Content OS, status "Approved for Ads":

- **Per vinkel:** 1 Single Image Ad + 1 Video Ad
- **Format-rekommendation:**
  - Smärta-fokus + Social proof = Video (känsla driver)
  - Lösnings-fokus + ROI = Single Image (siffror jämförelse)

```bash
# Upload creative + bind till ad set + bind Lead Gen Form
curl -X POST "https://api.linkedin.com/rest/adAccounts/$ACCOUNT/creatives" \
  -H "Authorization: Bearer $TOKEN" \
  -d @creative-payload.json
```

### Steg 6: Lookalike-expansion

```bash
curl -X POST "https://api.linkedin.com/rest/dmpSegments" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{
    "name": "Lookalike-ClosedWon-SE",
    "sourceSegmentUrn": "urn:li:dmpSegment:$CLOSED_WON_ID",
    "type": "LOOKALIKE",
    "country": "se"
  }'
```

Lägg till lookalike som **expansion-audience** på ad sets (inte primary) — LinkedIn använder den om primary-audience presterar dåligt.

### Steg 7: Sätt cap-regler

LinkedIn har ingen native auto-paus-regel — implementera via schemalagd Skill som kör varje 6:e timme och pausar ad sets där CPL > 3 000 kr efter ≥100 klicks.

```bash
# Pseudo-rule, kontrollera och pausa via API
claude run-skill linkedin-ads rule-check --adset $ADSET_ID
```

LinkedIn är dyrt — utan cap kan en dålig hook bränna 5 000 kr på en vecka.

## Output-format

Efter lyckad kampanjlansering:

```markdown
# LinkedIn-kampanj lanserad — [datum]

**Kampanj-ID:** <CAMPAIGN_ID>
**Total dagsbudget:** 800 kr/dag (4 ad sets × 200 kr)
**Custom Audience:** B2B-Marketers-SE-YYYYMMDD (X matchade profiler)
**Lookalike:** ClosedWon-SE som expansion
**Lead Gen Form:** B2B-Marketer-LeadGen-Form-2026

## Ad sets

| Vinkel | Ad set-ID | Format | Cap |
|---|---|---|---|
| Smärta-fokus | <ID> | Video | Paus om CPL >3000 kr @ 100 klicks |
| Lösnings-fokus | <ID> | Single Image | Paus om CPL >3000 kr @ 100 klicks |
| Social proof | <ID> | Video | Paus om CPL >3000 kr @ 100 klicks |
| ROI/data | <ID> | Single Image | Paus om CPL >3000 kr @ 100 klicks |

## Notering

Audience-matchning tar 48 timmar. Kampanjen är `PAUSED` tills matchning är klar — aktivera manuellt eller via scheduled task.
```

## Failure conditions

Avbryt och fråga användaren om:

- LinkedIn OAuth misslyckas (felaktiga scopes eller nekad app-godkännande) — felsök innan fortsättning
- Insight Tag är inaktiv på sajten — Modul 3 L3.2 behöver köras först
- Custom Audience har <300 matchade profiler — LinkedIn vägrar kampanj
- Användaren ber om budget <100 kr/dag per ad set — LinkedIn:s minimum är 200 kr för Lead Gen Forms
- Användaren ber om att skippa cap — neka, eftersom LinkedIn:s CPC är högt och risken för budget-brännande är reell

## References

- `references/funnel-architecture.md` — 3-layer funnel (cold/warm/hot) på LinkedIn med vinkel-mappning och format-rekommendationer per layer
