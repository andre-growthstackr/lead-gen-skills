---
name: crm-pipeline-mcp
description: Anslut HubSpot eller GoHighLevel via MCP och kör hela acquisition-funneln från Claude — lead in från lead magnet → CRM-record skapas → TOFU/MOFU/BOFU-sekvens triggas → veckorapport på måndag morgon. Skill:en orkestrerar marketing automation (email + sparsam SMS), pipeline-uppdateringar och leverans av en pipeline-rapport som mejl. Aktivera när användaren skriver "CRM pipeline", "HubSpot pipeline", "GoHighLevel pipeline", "veckans pipeline-rapport", "anslut CRM", "marketing automation flow", "TOFU MOFU BOFU-sekvens", eller refererar till L3.4 praktiska delen ("pipeline-rapport måndag morgon"). SMS används sparsamt — 1-2 per sekvens, aldrig spray. Skill:en kräver att HubSpot- eller GoHighLevel-MCP är ansluten i Claude Settings → Connectors.
---

# crm-pipeline-mcp

Skill för CRM-management via MCP. Claude läser, Claude skriver, du slipper kopiera datan manuellt. Tre primära jobb: (1) ta emot leads från lead magnets och routa till rätt sekvens, (2) köra TOFU/MOFU/BOFU email + SMS-sekvenser, (3) leverera veckorapport på måndag morgon.

## Vad skillen gör

- Anslut HubSpot eller GoHighLevel via MCP-connector i Claude
- Läs pipeline-stadier (lead → MQL → SQL → opportunity → closed/won)
- Skapa eller uppdatera CRM-records när leads kommer in från lead magnets
- Trigga TOFU/MOFU/BOFU-sekvens baserat på lead_magnet-värdet
- Skicka email + sparsam SMS i sekvens (1-2 SMS max per sekvens)
- Schemalägg pipeline-rapport (måndag 08:00) som mejl till användaren
- Rapporten täcker: leads som avancerat, leads som fastnat, follow-up-lista

## Vad skillen INTE gör

- Bygger inte lead magnet:en → `lead-magnet-builder-interactive`
- Bygger inte landningssidan → `landing-page-builder`
- Skriver inte själva email/SMS-copy från scratch — använder mallar i `references/automation-sequences.md` som start, men förväntar sig att copy är förberedd
- Skapar inte nya CRM-fält manuellt — användaren måste sätta upp custom fields i CRM:en innan skill:en kan pusha data dit

## Triggers

Aktivera vid:

- "CRM pipeline"
- "HubSpot pipeline"
- "GoHighLevel pipeline"
- "veckans pipeline-rapport"
- "anslut CRM" / "koppla in CRM"
- "marketing automation flow"
- "TOFU MOFU BOFU-sekvens"
- "pipeline-rapport måndag morgon" (L3.4 praktisk)
- "pusha leads till CRM"
- "trigga sekvens för [lead magnet]"

## Input som krävs

1. **CRM-val** — HubSpot eller GoHighLevel (bara en, inte båda samtidigt)
2. **MCP-anslutning verifierad** — Settings → Connectors i Claude visar grön status
3. **Custom fields i CRM:en** — minst:
   - `lead_magnet` (text) — vilken lead magnet leadet fyllde i
   - `score` (number) — score från quiz/assessment
   - `funnel_stage` (single-select: TOFU / MOFU / BOFU)
   - `last_activity` (datetime)
4. **Sekvens-mallar** — email + SMS-copy för TOFU/MOFU/BOFU (default-mallar finns i `references/automation-sequences.md`, anpassa till brand)
5. **Pipeline-stadier konfigurerade i CRM** — minst: Lead → MQL → SQL → Opportunity → Closed/Won

Om MCP inte är ansluten: stoppa, ge användaren steg för att ansluta (Settings → Connectors → HubSpot eller GoHighLevel → OAuth).

## Workflow A: Lead-intag från lead magnet

När en lead fyller i en lead magnet (från `lead-magnet-builder-interactive`):

1. Lead magnet:en POST:ar payload till webhook eller MCP-endpoint
2. Skill:en läser payload (email, lead_magnet, score, svar)
3. Slå upp om kontakten redan finns i CRM (på email)
   - Finns: uppdatera `lead_magnet`, `score`, `last_activity`. Kolla nuvarande `funnel_stage` — uppgradera om nya magneten är längre ner i funnel:en (TOFU → MOFU → BOFU).
   - Finns inte: skapa ny kontakt med `funnel_stage` matchande lead magnet:ens stadie.
4. Sätt kontakten i pipeline-stadie:
   - TOFU lead magnet → Pipeline: Lead
   - MOFU lead magnet → Pipeline: MQL
   - BOFU lead magnet → Pipeline: SQL
5. Trigga matchande sekvens (se Workflow B)
6. Logga aktiviteten i CRM som "Lead magnet submitted: [namn]"

## Workflow B: TOFU / MOFU / BOFU-sekvens

Tre sekvenser. Default-längd och cadence per stadie:

### TOFU-sekvens (5 email + 1 SMS, 14 dagar)

| Dag | Kanal | Innehåll |
|---|---|---|
| 0 | Email | Skicka resultatet/quizet + en mening om vad de ska göra härnäst |
| 2 | Email | Pain-djupgrävning: "Här är vad andra i din situation har gjort" |
| 5 | Email | Case study eller mini-content som bygger auktoritet |
| 8 | SMS | "Hej [förnamn], såg att du körde [quiz]. Snabb fråga: är [pain] något ni jobbar med nu?" |
| 10 | Email | Erbjudande: MOFU-magnet (ROI-kalkylator) |
| 14 | Email | Sista email — om ingen klick: pausa sekvens, behåll i nurture-loop |

### MOFU-sekvens (4 email + 1 SMS, 10 dagar)

| Dag | Kanal | Innehåll |
|---|---|---|
| 0 | Email | Skicka kalkylator-resultatet + en konkret rekommendation |
| 2 | Email | "Här är vad ditt resultat betyder i praktiken" + mini-content |
| 5 | SMS | "Hej [förnamn], vill du gå igenom resultatet tillsammans? 15 min, ingen pitch." |
| 7 | Email | Demo eller djupare gratis-resurs |
| 10 | Email | BOFU-magnet (personlig assessment) som CTA |

### BOFU-sekvens (3 email + 1 SMS, 7 dagar)

| Dag | Kanal | Innehåll |
|---|---|---|
| 0 | Email | Skicka assessment-rapporten + boka 30 min |
| 1 | SMS | "Såg din assessment, [förnamn]. Min kalender är öppen [datum]." |
| 3 | Email | Case study från liknande kund |
| 7 | Email | Direkt fråga: "Är det här rätt timing? Yes/no/senare" |

**SMS-regler — icke-förhandlingsbart:**
- Max 1-2 SMS per sekvens
- Aldrig SMS innan email-engagement (öppnat minst 1 email)
- SMS efter kl 20 eller före 09 lokal tid → skippas tills nästa morgon
- Opt-out måste vara explicit fungerande (STOPP → unsubscribe)

## Workflow C: Pipeline-rapport måndag morgon

Schemalägg via `mcp__scheduled-tasks__create_scheduled_task` med cron `0 8 * * 1` (måndag 08:00).

Rapporten genereras genom att Claude:

1. Läser alla pipeline-stadier via MCP
2. Jämför med förra veckans snapshot
3. Identifierar:
   - **Avancerat** — leads som rört sig nedåt i pipeline:n (Lead → MQL, MQL → SQL osv.)
   - **Fastnat** — leads som varit i samma stadie över X dagar (default: 7 för Lead, 14 för MQL, 14 för SQL)
   - **Follow-up** — leads med SMS-respons, email-reply eller hög score som inte fått personlig kontakt
4. Skriver rapport i markdown
5. Skickar som email till användaren

### Rapport-format

```markdown
# Pipeline-rapport — vecka [N]

**Period:** [måndag förra veckan] – [söndag förra veckan]  
**CRM:** HubSpot / GoHighLevel

## Sammanfattning

- Nya leads in: [X]
- Leads som avancerat: [Y]
- Leads som fastnat: [Z]
- Closed/Won denna vecka: [W]

## Avancerat (Y leads)

| Namn | Företag | Från → Till | Lead magnet | Vad triggade |
|---|---|---|---|---|
| ... | ... | Lead → MQL | T1-quiz | Klick på MOFU-email |

## Fastnat — behöver din uppmärksamhet (Z leads)

| Namn | Företag | Stadie | Dagar i stadiet | Senaste aktivitet | Föreslagen åtgärd |
|---|---|---|---|---|---|
| ... | ... | MQL | 16 | Öppnade email 12 dagar sedan | Skicka personligt email |

## Follow-up-lista (top 5)

1. **[Namn], [Företag]** — replyade på SMS dag 5. Boka 15 min.
2. ...

## Föreslagna åtgärder denna vecka

- Skicka 5 personliga email till fastnat-listan
- Boka in 3 follow-up-möten med follow-up-listan
- Granska MOFU-sekvens — 4 leads har inte engagerat sig i email 2
```

Email-leveransen sker via vald CRM (HubSpot transactional eller GoHighLevel) eller direkt SMTP.

## Output

- **Vid lead-intag:** uppdaterad CRM-kontakt + triggad sekvens + log-entry
- **Vid sekvens-körning:** schemalagda email/SMS i CRM:ens automation-flow
- **Vid måndag-rapport:** email till användaren + pipeline-snapshot sparad för nästa vecka

## Failure conditions

Stoppa och felrapportera om:

- MCP-anslutning är down — be användaren reansluta i Settings → Connectors
- Custom fields saknas i CRM — lista vilka som behöver skapas, gör inte själv
- Email-sekvens skickas till kontakter som inte opt-in:at (GDPR-brott)
- SMS skickas utanför tillåten tid (09-20 lokal tid)
- Pipeline-rapporten innehåller PII utöver namn/företag/email (ingen telefon, ingen adress, ingen branscharvad data) i klartext

## Anti-patterns

- "Spray-and-pray-sekvens" — alla leads får samma email oavsett källa. Bryt mot funnel-tänk.
- "5 SMS per vecka" — opt-outs spikar, brand bränns. Max 1-2 SMS per sekvens.
- "Automation utan hand-off" — när lead är SQL ska sekvensen pausa och människa ta över. Inte fortsätta drip:a.
- "Manuell pipeline-uppdatering" — om Claude inte kan läsa+skriva via MCP är skill:en värdelös, åter till CRM:ens UI.
- "Rapport utan åtgärdsförslag" — en lista över leads är inte en rapport, det är data. Föreslå konkreta åtgärder.

## References

- `references/automation-sequences.md` — fulla email + SMS-mallar för TOFU/MOFU/BOFU + variabel-tokens som ska bytas ut
- `references/mcp-setup-hubspot.md` — steg-för-steg att ansluta HubSpot MCP + custom fields-spec
- `references/mcp-setup-highlevel.md` — samma för GoHighLevel
- `references/pipeline-stage-mapping.md` — hur Lead/MQL/SQL/Opportunity/Closed/Won mappar mot dina lead magnets och funnel-stadier
- `references/sms-compliance.md` — opt-in/opt-out-flöden, tider, GDPR-noter

## Schemaläggning

För måndag-rapporten — använd `mcp__scheduled-tasks__create_scheduled_task` med:

- Cron: `0 8 * * 1` (måndag 08:00 svensk tid)
- Task: kör denna skill med flagga `--report-only`
- Output: email till användaren

Verifiera att schemat går igång nästa måndag innan du anser uppsättningen klar.
