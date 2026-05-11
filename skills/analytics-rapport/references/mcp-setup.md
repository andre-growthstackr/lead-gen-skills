# MCP-setup för analytics-rapport

Auth-steg per MCP. Kör en åt gången, verifiera med testfråga innan du går vidare till nästa.

---

## 1. Google Analytics 4 (GA4)

**MCP-paket:** `@google/analytics-mcp` eller community-version
**Docs:** https://developers.google.com/analytics/devguides/reporting/data/v1

### Setup

1. Skapa Service Account i Google Cloud Console:
   - APIs & Services → Credentials → Create Service Account
   - Roll: "Viewer" på GA4-property
2. Ladda ner JSON-key (sparas som `ga4-credentials.json`)
3. I GA4 Admin → Property Access Management — bjud in Service Account-emailen som Viewer
4. Notera Property ID (siffror, format `123456789`)

### Konfiguration

I `~/.claude/mcp-config.json`:

```json
{
  "ga4": {
    "command": "npx",
    "args": ["@google/analytics-mcp"],
    "env": {
      "GA4_CREDENTIALS_PATH": "/path/to/ga4-credentials.json",
      "GA4_PROPERTY_ID": "123456789"
    }
  }
}
```

### Verifiera

```bash
mcp__ga4__run_report \
  --dimensions "sessionDefaultChannelGroup" \
  --metrics "sessions" \
  --date-range "yesterday"
```

Förväntat: tabell med kanalgrupper och sessions-siffror.

---

## 2. Meta Ads

**MCP-paket:** Wrapper runt Meta Marketing API
**Docs:** https://developers.facebook.com/docs/marketing-apis

### Setup

1. Skapa System User i Meta Business Manager:
   - Business Settings → Users → System Users → Add
   - Roll: Admin på Ad Account
2. Generera Access Token med scope `ads_read`, `business_management`
3. Notera Ad Account ID (format `act_<numbers>`)

### Konfiguration

```json
{
  "meta-ads": {
    "command": "npx",
    "args": ["meta-ads-mcp"],
    "env": {
      "META_ACCESS_TOKEN": "EAAxxx...",
      "META_AD_ACCOUNT_ID": "act_xxx"
    }
  }
}
```

### Verifiera

```bash
mcp__meta-ads__metrics_get \
  --campaign all \
  --date-range yesterday \
  --metrics spend
```

Förväntat: lista med kampanjer och spend.

---

## 3. LinkedIn Ads

**MCP-paket:** ColdIQ:s LinkedIn Marketing-MCP eller community
**Docs:** https://learn.microsoft.com/en-us/linkedin/marketing/

### Setup

1. Skapa LinkedIn Developer App: https://www.linkedin.com/developers/
2. Lägg till Marketing Developer Platform-produkt (kräver review, kan ta 1-2 veckor)
3. Generera OAuth 2.0-token med scope `r_ads_reporting`, `r_organization_admin`
4. Notera Account URN (format `urn:li:sponsoredAccount:xxx`)

### Konfiguration

```json
{
  "linkedin-ads": {
    "command": "npx",
    "args": ["linkedin-ads-mcp"],
    "env": {
      "LINKEDIN_ACCESS_TOKEN": "AQxxx...",
      "LINKEDIN_ACCOUNT_URN": "urn:li:sponsoredAccount:xxx"
    }
  }
}
```

### Verifiera

```bash
mcp__linkedin-ads__metrics_get \
  --campaign all \
  --date-range yesterday \
  --metrics spend,leads
```

Förväntat: lista med kampanjer och spend/leads.

---

## 4. HubSpot (eller HighLevel)

**MCP-paket:** Officiellt HubSpot MCP
**Docs:** https://developers.hubspot.com/mcp

### Setup HubSpot

1. Skapa Private App i HubSpot:
   - Settings → Integrations → Private Apps → Create Private App
   - Scopes: `crm.objects.deals.read`, `crm.objects.contacts.read`
2. Notera Access Token

### Konfiguration HubSpot

```json
{
  "hubspot": {
    "command": "npx",
    "args": ["@hubspot/mcp"],
    "env": {
      "HUBSPOT_ACCESS_TOKEN": "pat-xxx..."
    }
  }
}
```

### Setup HighLevel (alternativ)

1. Skapa Sub-Account API-key i HighLevel:
   - Settings → Business Profile → API
2. Eller använd OAuth-app för multi-account access

### Konfiguration HighLevel

```json
{
  "highlevel": {
    "command": "npx",
    "args": ["highlevel-mcp"],
    "env": {
      "HIGHLEVEL_API_KEY": "xxx...",
      "HIGHLEVEL_LOCATION_ID": "xxx..."
    }
  }
}
```

### Verifiera

```bash
mcp__hubspot__search_objects \
  --object deals \
  --filter "dealstage=closedwon" \
  --limit 10
```

Förväntat: 10 senaste Closed/Won-deals med `hs_analytics_source`-fält ifyllt.

---

## 5. Stripe

**MCP-paket:** Stripe Agent Toolkit
**Docs:** https://github.com/stripe/agent-toolkit

### Setup

1. Generera Restricted Key i Stripe Dashboard:
   - Developers → API Keys → Create Restricted Key
   - Permissions: `customers:read`, `subscriptions:read`, `charges:read`
2. Notera Key (format `rk_live_xxx` eller `rk_test_xxx`)

### Konfiguration

```json
{
  "stripe": {
    "command": "npx",
    "args": ["@stripe/agent-toolkit", "mcp"],
    "env": {
      "STRIPE_API_KEY": "rk_live_xxx..."
    }
  }
}
```

### Verifiera

```bash
mcp__stripe__list_subscriptions \
  --status active \
  --limit 5
```

Förväntat: 5 aktiva subscriptions med MRR.

---

## Felsökning per MCP

| Symptom | Trolig orsak | Fix |
|---|---|---|
| `401 Unauthorized` | Token utgången/saknar scope | Generera ny token med rätt permissions |
| `403 Forbidden` | Service Account saknar Property/Account-access | Bjud in i resp. plattform |
| `Timeout` | MCP-paket inte installerat globalt | `npm install -g <paket>` |
| Tom respons | Datafönster har ingen data | Verifiera date-range, kolla aktivitet i platform-UI |
| `Rate limit exceeded` | För många anrop | Lägg in retry med exponential backoff |

---

## Säkerhetsanmärkningar

- Alla tokens sparas i `~/.claude/mcp-config.json` — fil-permissions ska vara `600`
- Service Accounts ska ha minst möjliga roll (Viewer/Read-only)
- Rotera tokens var 90:e dag
- Stripe-key ska vara **Restricted Key**, aldrig `sk_live_*`
