---
name: linkedin-outreach-cowork
description: Kör personaliserad LinkedIn-outreach via Claude Cowork (synligt Chrome-fönster) mot kvalificerade leads i Airtable (score > 70). Aktiveras varje gång användaren skriver "LinkedIn outreach", "connection requests", "LinkedIn outreach automation", "kör Cowork outreach", "skicka connection requests" eller refererar till L5.3 i kursen. Auto-researchar varje lead via Activity-fliken (senaste 5 posts), skriver connection request enligt formeln "referens till specifik post + bro + CTA" inom 300 tecken, kör 5-25 requests per dag, loggar allt i Airtable Outreach-tabell. Använd den även när användaren bara säger "connecta med leadsen" — skillen ÄR LinkedIn-motorn.
---

# linkedin-outreach-cowork

Skill för steg 3 i outbound-pipelinen: LinkedIn-outreach via Claude Cowork. Tar kvalificerade leads (score > 70 från L5.2), öppnar varje LinkedIn-profil i synligt Chrome-fönster, läser Activity-fliken, skriver personaliserad connection request, skickar, loggar. Volym-cappad och kvalitets-styrd.

## Vad skillen gör

- Hämtar kvalificerade leads från Airtable Leads-tabell (`status = "kvalificerad"` AND `score > 70`)
- Öppnar Claude Cowork mot Chrome (synligt fönster så användaren kan följa)
- För varje lead: navigerar till LinkedIn-profil → Activity-flik → läser senaste 5 posts
- Genererar personaliserad connection request enligt formeln (ref + bro + CTA, max 300 tecken)
- Skickar requesten via UI
- Loggar action i Airtable Outreach-tabell
- Triggar tvåstegs uppföljnings-flöde när lead accepterar

## Vad skillen INTE gör

- Bygga lista → `list-building-wrapper`
- Berika/scora → `lead-enrichment-scoring`
- Cold email → `cold-email`
- Generic LinkedIn-aktivitet (likes, comments) — bara outreach

## Triggers

Aktivera när användaren skriver något av följande:

- "LinkedIn outreach"
- "connection requests"
- "LinkedIn outreach automation"
- "kör Cowork outreach"
- "skicka connection requests"
- "connecta med leadsen"
- "L5.3" eller "lektion 5.3"

## Grundregel: research-loop, inte fältsubstitution

Mall-personalisering med `{first_name}` är mail merge från 1998. Det fungerar inte längre.

Personalisering = research per lead → specifik referens till något lead:et faktiskt sagt eller gjort. Auto-research av Activity-fliken är core. Utan färsk post-referens skickas ingen request.

## Hård gräns: 300 tecken

LinkedIn mobile-preview = 2 rader. Längre = ser ut som säljbrev = ignoreras.

Skillen räknar tecken före skickande. Över 300 = omskrivning.

## Workflow

### 1. Hämta kvalificerade leads

Via Airtable MCP:
```
list_records_for_table där status = "kvalificerad" AND score > 70 AND outreach_sent_at IS NULL
```

Sortera på score descending.

### 2. Sätt upp Cowork-session

Öppna Claude Cowork med Chrome synligt. Användaren ska se vad som händer (det är en del av kursens demo-värde och låter användaren stänga av om något går snett).

### 3. Per lead-loop

För varje lead i listan (max 25 per dag):

**a) Navigera**
- Öppna `linkedin_url` från Airtable-raden
- Vänta tills profilen laddat

**b) Auto-research**
- Klicka Activity-fliken
- Skrolla för att läsa senaste 5 posts
- Extrahera: text, datum, engagement-nivå
- Identifiera ett specifikt påstående/insikt att referera

**c) Skriv connection request**

Format (formel):
```
[Referens till specifik post] + [Bro till varför du connectar] + [CTA]
```

Exempel (skriv ALDRIG av denna mall, använd som referens):
```
Såg din post om churn-mätning i SaaS — håller med om att MRR-cohorts missar det viktigaste.
Bygger något i samma riktning. Connecta?
```

285 tecken. Specifik. Inget {first_name}-mall-stuk.

Inspirations-mallar (NOT copy-paste) finns i `references/message-templates.md`.

**d) Räkna tecken**

Om > 300 tecken → kör Claude-omskrivning som komprimerar utan att tappa specifik referens. Om fortfarande > 300 efter två försök → flagga lead som "för komplext för auto" och hoppa över.

**e) Visa för användaren** (om interaktivt läge)

I första 5-10 requestsen, paus och be André godkänna texten. Efter det → auto-skickande tills volym-cap.

**f) Skicka**

Klicka Connect → Add a note → klistra in text → Send.

**g) Logga i Airtable Outreach-tabell**

```
{
  "lead_id": "<lead-id>",
  "channel": "linkedin",
  "action": "connection_request_sent",
  "message": "<request-text>",
  "char_count": <antal>,
  "sent_at": "<ISO>",
  "post_referenced": "<URL till post>"
}
```

Uppdatera även Leads-tabell: `outreach_sent_at = <ISO>`, `outreach_channel = "linkedin"`.

### 4. Volym-grindvakter

- Max **25 connection requests per dag** (LinkedIn-skydd)
- Min **30% accept-rate** över 7 dgr — under det, pausa och kör kvalitets-review
- 2-5 sekunder pause mellan requests (mänskligt tempo)

### 5. Tvåstegs uppföljnings-flöde

När lead accepterar (kolla en gång per dag via Cowork):

**Steg 1 — direkt efter accept (samma dag):**
- Kort tack-meddelande, ingen pitch
- Referera tillbaka till connection-request-kroken
- Max 200 tecken

Exempel:
```
Tack för accept. Apropå det vi snackade om — nyfiken om ni kör cohort-analys idag eller bara revenue-snapshot?
```

**Steg 2 — 3 dagar senare om de svarat:**
- Erbjudande om kort samtal eller dela resurs
- Konkret CTA (kalenderlänk eller specifik fråga)
- Max 400 tecken

Om de inte svarat efter steg 1 → ingen steg 2. Markera `nurture` och vänta 30 dgr.

### 6. Sammanfattning per session

```
Session avslutad.
- 18 connection requests skickade
- 6 lead-profiler för komplexa (flaggade för manuell hantering)
- 1 lead saknade nyligen postad aktivitet (skippad)
- Genomsnittlig längd: 268 tecken
- Loggade i Airtable Outreach-tabell

Nästa kontroll-tillfälle: imorgon kl 09:00 för accept-rate + steg 1-uppföljning.
```

## Output-format

- **Airtable Outreach-tabell**: en rad per skickad request med fält ovan
- **Airtable Leads-tabell**: uppdaterad med `outreach_sent_at` + `outreach_channel`
- **Session-rapport**: ord-sammanfattning till användaren

## Failure conditions

- LinkedIn detekterar bot-beteende → pausa omedelbart, rapportera till användaren, vänta 24h
- Profil är dold för icke-connections → flagga lead som "behöver mutual" och skippa
- Activity-fliken är tom (lead postar aldrig) → skippa, markera "ingen referens-grund"
- Request > 300 tecken efter 2 omskrivningar → skippa lead, flagga manuell hantering
- Airtable MCP svarar inte → batcha lokalt, sync när det är tillbaka

## Anti-patterns

- `{first_name}-mallar` — förbjudet
- Generisk "Hi, would love to connect" — förbjudet
- Skicka utan att läsa Activity-fliken — förbjudet
- Volym över 25/dag — LinkedIn-ban-risk
- Auto-skicka utan första 5-10 godkänd-loop med André — bygger inte tillit till skillen

## References

- `references/message-templates.md` — Inspirations-mallar (referens, INTE copy-paste-mall)
