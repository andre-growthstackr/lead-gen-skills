---
name: linkedin-ads-coldiq-wrapper
description: Wrapper runt ColdIQ's LinkedIn Ads-skill med kursens egen branding och workflow. Klonar ColdIQ-repo, installerar Skill i Claude Code, autentiserar mot LinkedIn Campaign Manager, laddar upp Custom Audience (Closed/Won + Signal-Scraping-profiler), skapar Lead Gen Forms-kampanj med 4 ad sets per vinkel, konfigurerar Lookalike och sätter cap. Använd skillen varje gång användaren skriver "LinkedIn Ads", "LinkedIn Lead Gen Forms", "LinkedIn-kampanj", "ColdIQ LinkedIn", eller vill köra LinkedIn-annonsering programmatiskt. Pekar tydligt till ColdIQ för underliggande implementation.
---

# linkedin-ads-coldiq-wrapper

Wrapper-skill som integrerar ColdIQ:s LinkedIn Ads Skill i Modul 6:s leadgen-pipeline. ColdIQ har redan byggt den underliggande LinkedIn Marketing API-integrationen — denna skill ärver den och lägger till kursens kampanjstruktur, vinkel-mappning, och cap-regler.

## Vad skillen gör

- Klonar ColdIQ GTM Skills GitHub-repo och installerar LinkedIn Ads Skill i Claude Code
- Autentiserar mot LinkedIn Campaign Manager + verifierar Insight Tag från Modul 3
- Laddar upp Custom Audience: Closed/Won från CRM + 2 000+ profil-URLs från Signal-Scraping
- Skapar Lead Gen Forms-kampanj: 4 ad sets (en per vinkel från `ad-ideation-4-angles`)
- Lägger till Single Image + Video-ads från Airtable Content OS
- Konfigurerar Lookalike på Closed/Won som expansion
- Sätter cap: 200 kr/dag per ad set, paus om CPL >3 000 kr efter 100 klicks

## Vad skillen INTE gör

- Bygger inte LinkedIn-integrationen från noll — ColdIQ:s repo gör jobbet
- Producerar inte creatives — L4.2 (video) och L4.3 (image) gör det
- Skriver inte vinklar — `ad-ideation-4-angles` gör det
- Hanterar inte Meta — `meta-ads-cli` gör det

## Triggers

Aktivera när användaren skriver:

- "LinkedIn Ads"
- "LinkedIn Lead Gen Forms"
- "LinkedIn-kampanj"
- "ColdIQ LinkedIn"
- "LinkedIn programmatiskt"
- "kör LinkedIn-annonsering"

## Underliggande implementation: ColdIQ

Denna skill är en wrapper. Den verkliga integrationen ligger i:

**Repo:** https://github.com/sachacoldiq/ColdIQ-s-GTM-Skills
**Skill-path i repot:** `master-skills/linkedin-ads/`

Om något i den underliggande implementationen krånglar — felsökning sker mot ColdIQ:s dokumentation och issues, inte mot denna wrapper.

## Workflow

### Steg 1: Klona och installera ColdIQ Skill

```bash
cd ~/.claude/skills/
git clone https://github.com/sachacoldiq/ColdIQ-s-GTM-Skills.git coldiq-gtm
ln -s ~/.claude/skills/coldiq-gtm/master-skills/linkedin-ads ./linkedin-ads
```

Verifiera installation:

```bash
claude skills list | grep linkedin-ads
```

### Steg 2: Autentisera mot LinkedIn Campaign Manager

ColdIQ:s skill triggar OAuth-flödet:

```bash
claude run-skill linkedin-ads auth
```

Användaren godkänner manuellt i webbläsare. Tokenet sparas i `~/.claude/coldiq-credentials/linkedin.json`.

Verifiera Insight Tag (från Modul 3 L3.2) genom att besöka Campaign Manager → Account Assets → Insight Tag. Status ska vara "Active".

### Steg 3: Custom Audience-upload

Två källor:

**Källa A — Closed/Won från CRM:**

```bash
# Export via HubSpot MCP
mcp__hubspot__search_objects --filter "dealstage=closedwon" \
  --properties "email,linkedin_url" > closedwon.csv
```

**Källa B — Signal-Scraping-profiler:**

Från `signal-scraping`-skillen i Modul 3 — engagers på din nischs LinkedIn-content. 2 000+ profil-URLs i `signal-profiles.csv`.

Kombinera och pusha:

```bash
claude run-skill linkedin-ads audience-upload \
  --files closedwon.csv,signal-profiles.csv \
  --name "B2B-Marketers-SE-YYYYMMDD" \
  --type matched_audience
```

LinkedIn behöver 48 timmar för att matcha profiler — vänta innan kampanjlansering.

### Steg 4: Skapa Lead Gen Forms-kampanj

Funnel-arkitekturen (3-layer cold/warm/hot) beskrivs i `references/funnel-architecture.md`. Läs den innan första kampanjlansering.

```bash
claude run-skill linkedin-ads campaign-create \
  --name "LeadGen-LinkedIn-Q[N]" \
  --objective LEAD_GENERATION \
  --type LEAD_GEN_FORMS \
  --audience B2B-Marketers-SE-YYYYMMDD
```

Skapa 4 ad sets — en per vinkel från `ad-ideation-4-angles`:

```bash
for VINKEL in smarta losning social-proof roi; do
  claude run-skill linkedin-ads adset-create \
    --campaign <CAMPAIGN_ID> \
    --name "AS-$VINKEL" \
    --daily-budget 200 \
    --currency SEK
done
```

### Steg 5: Lägg till creatives

Från Airtable Content OS, status "Approved for Ads":

- **Per vinkel:** 1 Single Image Ad + 1 Video Ad
- **Format-rekommendation:**
  - Smärta-fokus + Social proof = Video (känsla driver)
  - Lösnings-fokus + ROI = Single Image (siffror jämförelse)

```bash
claude run-skill linkedin-ads creative-upload \
  --adset <ADSET_ID> \
  --format SINGLE_IMAGE \
  --media ./image-smarta-v1.jpg \
  --headline "[Hook]" \
  --intro "[Body]" \
  --cta "LEARN_MORE" \
  --lead-gen-form <FORM_ID>
```

Lead Gen Form skapas separat (görs en gång, återanvänds):

```bash
claude run-skill linkedin-ads form-create \
  --name "B2B-Marketer-LeadGen-Form-2026" \
  --fields "first_name,last_name,email,company,job_title" \
  --thank-you-url https://growthstackr.io/tack
```

### Steg 6: Lookalike-expansion

```bash
claude run-skill linkedin-ads audience-lookalike \
  --source <CLOSED_WON_AUDIENCE_ID> \
  --name "Lookalike-ClosedWon-SE" \
  --country SE
```

Lägg till lookalike som **expansion-audience** på ad sets (inte primary) — LinkedIn använder den om primary-audience presterar dåligt.

### Steg 7: Sätt cap-regler

Konfigurera auto-paus per ad set:

```bash
claude run-skill linkedin-ads rule-create \
  --adset <ADSET_ID> \
  --condition "cpl > 3000 AND clicks > 100" \
  --action PAUSE
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

## Underliggande

Implementation: ColdIQ GTM Skills (https://github.com/sachacoldiq/ColdIQ-s-GTM-Skills)
```

## Failure conditions

Avbryt och fråga användaren om:

- ColdIQ-repo inte går att klona (network/auth issue) — felsök innan fortsättning
- Insight Tag är inaktiv på sajten — Modul 3 L3.2 behöver köras först
- Custom Audience har <300 matchade profiler — LinkedIn vägrar kampanj
- Användaren ber om budget <100 kr/dag per ad set — LinkedIn:s minimum är 200 kr för Lead Gen Forms
- Användaren ber om att skippa cap — neka, eftersom LinkedIn:s CPC är högt och risken för budget-brännande är reell

## References

- `references/funnel-architecture.md` — 3-layer funnel (cold/warm/hot) på LinkedIn med vinkel-mappning och format-rekommendationer per layer
