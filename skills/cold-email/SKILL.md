---
name: cold-email
description: Kör cold email-outreach mot kvalificerade leads (score > 70 från L5.2) via sekundärdomän med korrekt SPF/DKIM/DMARC-setup, trigger-baserad personalisering och AI Appointment Setter på inkorgen. Aktiveras varje gång användaren skriver "cold email", "cold outreach email", "sekundärdomän email", "kör cold email", "email outreach", "Instantly setup", "appointment setter" eller refererar till L5.4 i kursen. Stöder två produktionsvägar: Instantly (färdigt verktyg, snabb start, hanterar warmup) eller Claude Code/Cowork direkt (mer kontroll, mer DIY). Hela grundregeln är trigger + speed + personalisering + sekundärdomän — alla fyra eller 5% reply rate istället för 18%. Använd den även när användaren bara säger "vi behöver email-utskick till leadsen" — skillen ÄR cold-email-motorn.
---

# cold-email

Skill för steg 4 i outbound-pipelinen: cold email till kvalificerade leads. Hanterar hela kedjan från sekundärdomän-setup till skickande till AI Appointment Setter på inkorgen. Skillen ärver `brand-guidelines.skill.md` från Modul 2 för voice i email-copy.

## Vad skillen gör

- Sätter upp sekundärdomän med 301-redirect till huvuddomän + SPF/DKIM/DMARC
- Konfigurerar email-warmup (4-6 veckor volym-ramp)
- Detekterar triggers per lead (funding-news, summit-attendance, ny VP, jobbannons)
- Genererar trigger-baserad email-copy med Claude (ärver brand voice)
- Skickar via Instantly ELLER Claude Code/Cowork direkt
- Aktiverar AI Appointment Setter som auto-respond inom 30 sek på inkommande intent

## Vad skillen INTE gör

- Bygga rålista → `list-building`
- Berika/scora → `lead-enrichment-scoring`
- LinkedIn-outreach → `linkedin-outreach-cowork`
- Hantera huvuddomän-konfiguration — sekundärdomän är skyddslagret

## Triggers

Aktivera när användaren skriver något av följande:

- "cold email"
- "cold outreach email"
- "sekundärdomän email"
- "kör cold email"
- "email outreach"
- "Instantly setup"
- "appointment setter"
- "L5.4" eller "lektion 5.4"

## Grundregeln: fyra delar krävs

Alla fyra eller ingen affär. En saknas → 5% reply rate (industristandard). Alla fyra → 18%+ reply rate.

| Del | Vad det betyder |
|---|---|
| **Trigger** | Färsk företagsnyhet som hook (funding, summit, ny VP, jobbannons) |
| **Speed** | Skicka inom 7 dagar efter trigger — efter det är det inte längre färskt |
| **Personalisering** | Email refererar till triggern specifikt, inte "I noticed your company" |
| **Sekundärdomän** | Huvuddomänen skyddas, sekundärdomän bär all cold-volym |

Om en saknas → skillen stannar och frågar.

## Workflow

### Fas 1: Infrastruktur (engångsuppsättning, kör en gång per ny sekundärdomän)

#### Steg 1.1: Sätt upp sekundärdomän

- Registrera domän som liknar huvuddomänen (t.ex. huvuddomän = `bolaget.se`, sekundär = `bolaget.io` eller `getbolaget.se`)
- 301-redirect från sekundärdomän → huvuddomän (för trovärdighet om mottagare klickar)
- Sekundärdomän får aldrig användas för transaktionell email — bara cold outreach

#### Steg 1.2: SPF/DKIM/DMARC

Konsultera `references/deliverability-setup.md` för exakta DNS-poster. Sätt:

- **SPF**: TXT-record som tillåter din ESP att skicka i sekundärdomänens namn
- **DKIM**: TXT-record med din ESP-genererade public key
- **DMARC**: TXT-record med policy `p=quarantine` och rapporterings-adress

Verifiera via `mxtoolbox.com` eller motsvarande att alla tre passerar.

#### Steg 1.3: Email-warmup (4-6 veckor)

- Använd Instantly's inbyggda warmup ELLER Mailwarm/Lemwarm
- Starta på 5 email/dag, öka 5/dag per vecka
- Mål-volym efter 6 veckor: 30-50 email/dag/inbox
- Om reputation-score dippar → pausa, vänta 7 dgr, återstarta lägre

**Förbjudet:** skicka cold-volym (>10/dag) från en oxan inbox. Brinner sekundärdomänen och du måste byta.

### Fas 2: Produktionsval (per kampanj)

Två vägar. Välj baserat på behov:

#### Väg A: Instantly

**När:** snabb start, mindre DIY, vill ha warmup hanterat åt dig

**Workflow:**
1. Koppla sekundärdomän till Instantly
2. Importera kvalificerade leads från Airtable (score > 70)
3. Kör denna cold-email Skill i Claude Code för att generera trigger-baserad copy per lead
4. Importera generated copy till Instantly som personalised campaign
5. Konfigurera sändnings-rytm (max 30-50/dag/inbox)
6. Starta kampanj
7. Monitorera reply-rate dagligen

#### Väg B: Claude Code/Cowork direkt

**När:** mer kontroll, custom-flow, eller integration med befintlig Gmail/Outlook-MCP

**Workflow:**
1. Koppla Gmail-MCP eller Outlook-MCP i Claude Code/Cowork
2. Kör Cold Email Skill mot kvalificerad lista från Airtable
3. Claude genererar + skickar email autonomt, en åt gången
4. Logga varje skickat email i Airtable Outreach-tabell
5. Hard cap: 30/dag/inbox

### Fas 3: Trigger-detektion (per lead)

För varje kvalificerad lead, detektera trigger via `enrichment_data` (från L5.2):

| Trigger | Datakälla | Färskhet |
|---|---|---|
| Funding-runda | Crunchbase, news | Senaste 30 dgr |
| Summit-attendance | LinkedIn-posts, event-sites | Senaste 14 dgr |
| Ny VP/Director | LinkedIn-feed, news | Senaste 60 dgr |
| Jobbannons | LinkedIn Jobs, hemsida-careers | Senaste 14 dgr |
| Produktlansering | Hemsida, ProductHunt, news | Senaste 30 dgr |
| Expansion (region/team) | LinkedIn-posts, news | Senaste 30 dgr |

Om ingen trigger → skicka inte. Flagga lead som "väntar på trigger", check igen om 14 dgr.

Detaljerade prompts per trigger-typ finns i `references/trigger-prompts.md`.

### Fas 4: Email-generation per lead

Använd denna Skill men injicera:

1. Trigger som hook (första 1-2 meningar)
2. Bro till varför du skriver (1 mening)
3. Specifikt erbjudande (1-2 meningar)
4. Lätt CTA (1 mening)

Total length: 60-90 ord. Inte mer. Längre email läses inte.

Voice: ärv från `brand-guidelines.skill.md`. Naturlig svenska/engelska, ingen företagsröst, inga "thrilled to share"-fraser.

### Fas 5: AI Appointment Setter

Aktivera på inkorgen för auto-respond inom 30 sek på inkommande intent. Detaljerad prompt i `references/appointment-setter-prompt.md`.

**Logik:**
- Klassificera inkommande svar: intent / fråga / inte intresserad / opt-out
- Vid intent: svara med bokningslänk + en mening kontext, inom 30 sek
- Vid fråga: svara på frågan + bokningslänk
- Vid inte intresserad: artigt tack, ingen retry
- Vid opt-out: respektera, markera Airtable, aldrig kontakta igen

## Output-format

- **Airtable Outreach-tabell**: rad per skickad email med fält `lead_id`, `channel="email"`, `subject`, `body`, `trigger_type`, `sent_at`
- **Airtable Leads-tabell**: uppdatera `outreach_sent_at`, `outreach_channel`
- **Daglig rapport**: skickade idag, reply-rate kumulativt, top-3 triggers som funkade

## Failure conditions

- Sekundärdomän inte konfigurerad → stoppa, kör Fas 1
- SPF/DKIM/DMARC misslyckas verifiering → stoppa, fix DNS
- Warmup-fas inte klar → stoppa, vänta
- Ingen trigger för en lead → skippa, flagga "väntar"
- Reply-rate < 5% efter 100 emails → pausa, kvalitets-review
- Bounce-rate > 5% → pausa, dubbelkolla email-verifiering i L5.2

## Volym & disciplin

- Max 30-50 email/dag/inbox
- Max 3 inboxar per sekundärdomän
- Skicka måndag-torsdag 09-15 lokal tid för mottagaren
- Aldrig samma copy till flera leads — varje email är unikt genererat per trigger

## Anti-patterns

- "I hope this email finds you well" — direkt spam-signal
- "Quick question" — körts ihjäl, ignoreras
- Bilagor — ökar spam-score
- Trackpixels — ökar spam-score, mottagare hatar det
- Skicka från huvuddomän → 6-12 mån reputation-skada om det går snett

## Inherit

- `brand-guidelines.skill.md` (Modul 2) — voice för all email-copy

## References

- `references/deliverability-setup.md` — SPF/DKIM/DMARC step-by-step
- `references/trigger-prompts.md` — Per trigger-typ: hur du skriver hook
- `references/appointment-setter-prompt.md` — Prompt för AI Appointment Setter på inkorgen
