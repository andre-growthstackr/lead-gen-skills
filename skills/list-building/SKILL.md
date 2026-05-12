---
name: list-building
description: Bygg en kvalificerad B2B-leadslista (50-100 leads) genom ICP-driven käll-väljare och Airtable-import. Aktiveras varje gång användaren skriver "bygg leadslista", "list building", "sammanställ leads", "scrapa leads", "kör list building" eller refererar till L5.1 i kursen. Skillen läser `icp-summary.md` från Project, väljer rätt lead-källa (Apify, Google Maps, Apollo.io, Aleads) baserat på ICP, kör scrape, strukturerar output och dumpar leadsen i Airtable Leads-tabell via MCP. Använd den även när användaren bara säger "jag behöver leads" utan att specificera källa — skillen ÄR käll-väljaren.
---

# list-building

Skill för att bygga steg 1 i outbound-pipelinen: råleadslistan. Den orkestrerar käll-val, scrape-konfig och Airtable-import till en enda kommando-koreografi. När den körts klart har Airtable Leads-tabellen 50-100 strukturerade leads redo för berikning i L5.2.

## Vad skillen gör

- Läser `icp-summary.md` från aktivt Claude Project för kvalifikationskriterier
- Väljer rätt lead-källa (Apify, Google Maps, Apollo.io, Aleads) baserat på ICP-typ
- Konfigurerar scrape-parametrar mot vald källa
- Kör scraping och får 50-100 leads som CSV/JSON
- Importerar leadsen till Airtable Leads-tabell via Airtable MCP
- Lämnar listan deduplicerad och kolumn-strukturerad

## Vad skillen INTE gör

- Berikning eller scoring → `lead-enrichment-scoring`
- LinkedIn-outreach → `linkedin-outreach-cowork`
- Cold email → `cold-email`
- ICP-definition → görs i Modul 2, läses som input här

Om användaren ber om något av ovan: hänvisa till rätt skill.

## Triggers

Aktivera när användaren skriver något av följande (svenska eller engelska):

- "bygg leadslista"
- "list building"
- "sammanställ leads"
- "scrapa leads"
- "kör list building"
- "jag behöver en leadslista"
- "L5.1" eller "lektion 5.1"

## Källval — fyra vägar

Skillen läser `icp-summary.md` och matchar mot dessa fyra källor. Detaljerad logik finns i `references/source-selection.md`.

| Källa | När den passar | Output |
|---|---|---|
| **Apify** | B2B SaaS/tech-ICP, behöver LinkedIn-profiler eller Crunchbase-data | Strukturerad JSON från actor |
| **Google Maps** | Local services, retail, restauranger, fysiska företag | CSV med namn, adress, telefon, hemsida |
| **Apollo.io** | Generic B2B (utan tech-fokus), behöver email + firmografi | CSV med 50-100 leads och email |
| **Aleads** | Strukturerad data behövs snabbt, mindre research-tid | API-output, redan strukturerad |

Default-prioritering om ICP är oklar: fråga André innan du gissar.

## Workflow

### 1. Läs ICP-spec

```
Read icp-summary.md från aktivt Claude Project
```

Identifiera:
- Bransch (SaaS/tech vs local services vs generic B2B)
- Geografi (Sverige? Norden? Global?)
- Företagsstorlek (1-10, 11-50, 51-200, 200+)
- Decision-maker-titel (VP Marketing, Head of Growth, Founder, etc.)

### 2. Välj källa

Konsultera `references/source-selection.md` och matcha ICP mot rätt källa. Om matchning är otydlig — fråga André innan du fortsätter.

### 3. Konfigurera scrape-parametrar

Per källa:

**Apify**: välj actor (LinkedIn Profile Scraper, Crunchbase Scraper, etc.), search-query (t.ex. "VP Marketing site:linkedin.com/in/ Sverige"), max results = 100. Apify MCP körs direkt från Claude Code om den är ansluten.

**Google Maps**: keyword (t.ex. "tandläkare Stockholm"), radius, max results = 100. Apify Google Maps Scraper-actor eller Google Maps Places API.

**Apollo.io**: filter på title, industry, location, company size; max results = 100. Apollo-API via Apollo MCP eller manuell export.

**Aleads**: API-call med ICP-kriterier som parameter.

### 4. Kör scrape

Exekvera vald scraper med konfigurerade parametrar. Vänta på output (CSV/JSON).

### 5. Strukturera & dedupliera

Säkerställ att output har dessa kolumner som minimum:
- `lead_id` (genereras om saknas)
- `first_name`
- `last_name`
- `title`
- `company`
- `linkedin_url`
- `email` (om tillgängligt — annars tomt, fylls i L5.2)
- `website`
- `source` (apify | google_maps | apollo | aleads)
- `scraped_at` (ISO timestamp)

Dedupliera på `linkedin_url` (primärt) eller `email` + `company` (sekundärt).

### 6. Importera till Airtable

Använd Airtable MCP-verktyg (`mcp__8e100d6a-...__create_records_for_table`) för att skriva leads till Leads-tabellen i base `appVPVpJWkvTOUBop` (eller den base som anges i Project).

Sätt `status = "ny"` på alla nya rader.

### 7. Verifiera

Logga till användaren:
- Antal leads importerade
- Antal dubbletter borttagna
- Antal med email vs utan email
- Vilken källa som användes

## Output-format

Två leveranser per körning:

1. **CSV/JSON-fil** lokalt: `leads-YYYY-MM-DD.csv` med alla scrape:ade leads inklusive raw fields
2. **Airtable Leads-tabell** uppdaterad: nya rader med standardkolumnerna ovan + `status = "ny"`

Avslutande sammanfattning till användaren:

```
Klart. 78 leads in i Airtable Leads-tabell.
- Källa: Apify (LinkedIn search)
- 12 dubbletter borttagna
- 64/78 har email, 14 behöver Waterfall i L5.2
- Filen sparad som leads-2026-05-11.csv

Nästa steg: kör lead-enrichment-scoring för att berika och poängsätta.
```

## Failure conditions

Skillen stannar och frågar användaren om:

- `icp-summary.md` saknas i Project — be André skapa eller peka ut
- Käll-val är otydligt mellan två källor — fråga vilken som föredras
- Scrape-verktyg svarar inte — kontrollera Apify-token, Apollo-credentials eller annan auth
- Scrape returnerar färre än 20 leads — be om bredare ICP-kriterier
- Airtable MCP svarar inte — kontrollera att MCP-server är ansluten

Skillen rapporterar fel men gör inga gissningar utanför ICP-spec.

## Volym-grindvakt

Hård gräns per körning:

- Max 100 leads per scrape (overflow = ny körning)
- Max 200 leads per dag totalt mot samma källa (rate-limit-skydd)
- Min 30 leads för att fortsätta — under det, fråga om bredare ICP

## References

- `references/source-selection.md` — När varje källa passar (detaljerad logik, ICP-mappning, exempel)
