# Automation-sekvens-mallar — TOFU / MOFU / BOFU

Mallar för email + SMS-sekvenser som triggas när en lead fyller i en lead magnet. Inherit:a `brand-guidelines.skill.md` för voice. Variabler i `{{}}` byts ut per kampanj.

**Regler:**

- SMS max 1-2 per sekvens
- Aldrig SMS innan email-engagement (öppnat minst 1 email)
- SMS bara mellan 09-20 lokal tid
- Opt-out måste fungera (STOPP → unsubscribe)
- Sekvensen pausar automatiskt om leadet replyar eller bokar möte

---

## TOFU-sekvens (5 email + 1 SMS, 14 dagar)

Trigger: leadet fyllde i en TOFU lead magnet (pain awareness-quiz eller checklista).

### Email 1 — Dag 0 (omedelbart)

**Subject:** Här är ditt resultat — {{LEAD_MAGNET_TITLE}}

```
Hej {{FIRST_NAME}},

Här är ditt resultat: {{RESULT_CATEGORY}}.

Det betyder i praktiken: {{ONE_LINE_INTERPRETATION}}.

Nästa steg — kolla bilagan eller länken nedan. Och om något känns oklart, replya direkt på det här mejlet. Det är jag som läser.

{{SIGNATUR}}
```

### Email 2 — Dag 2

**Subject:** Det här är det vanligaste misstaget vi ser

```
Hej {{FIRST_NAME}},

I ditt resultat såg jag att {{SPECIFIC_PAIN_FROM_QUIZ}}.

Det är inte ovanligt. Vi ser samma sak hos {{X}} av {{Y}} företag som kör samma quiz.

Det vanligaste misstaget de gör: {{COMMON_MISTAKE}}.

Och det fungerar inte. Här är varför: {{WHY_NOT}}.

Vad funkar istället: {{WHAT_WORKS}}.

— {{SIGNATUR}}
```

### Email 3 — Dag 5

**Subject:** Hur {{CASE_COMPANY}} löste det

```
Hej {{FIRST_NAME}},

Förra året jobbade vi med {{CASE_COMPANY}}, ett {{SIZE}} bolag i {{INDUSTRY}}.

De hade exakt samma problem som ditt quiz pekade på.

Det vi gjorde: {{THREE_BULLETS_WHAT_WAS_DONE}}.

Resultatet: {{SPECIFIC_OUTCOME_WITH_NUMBER}}.

Hela case studyn finns här: {{LINK}}

— {{SIGNATUR}}
```

### SMS — Dag 8 (om leadet öppnat minst 1 email)

```
Hej {{FIRST_NAME}}, såg att du körde {{LEAD_MAGNET_TITLE}}. Snabb fråga: är {{PAIN}} något ni jobbar med just nu? — {{FIRST_NAME_FROM_SENDER}}
```

Max 160 tecken. Inget länk i SMS. Inget pitch. Bara en fråga.

### Email 4 — Dag 10

**Subject:** Vill du räkna ut vad det kostar?

```
Hej {{FIRST_NAME}},

Du har sett vad problemet är. Nästa steg är att veta vad det faktiskt kostar dig.

Jag byggde en kalkylator som tar 90 sekunder att fylla i — du får en siffra på vad {{PAIN}} kostar ditt bolag varje månad.

Här är den: {{MOFU_MAGNET_URL}}

Om siffran skrämmer dig — bokar du tid med oss. Om den känns ok — kasta mejlet och fortsätt på.

— {{SIGNATUR}}
```

### Email 5 — Dag 14 (sista)

**Subject:** Sista mejlet i den här serien

```
Hej {{FIRST_NAME}},

Jag har skickat fem mejl. Du har inte klickat dig vidare.

Det är helt okej. Timing är allt.

Jag pausar serien nu. Om du vill att vi pratar senare — replya på det här mejlet med "ja, senare" så hör jag av mig om 3 månader.

— {{SIGNATUR}}
```

---

## MOFU-sekvens (4 email + 1 SMS, 10 dagar)

Trigger: leadet fyllde i en MOFU lead magnet (ROI-kalkylator eller jämförelse-tool).

### Email 1 — Dag 0

**Subject:** Din uträkning — {{LEAD_MAGNET_TITLE}}

```
Hej {{FIRST_NAME}},

Din siffra: {{CALCULATED_VALUE}} kr/år.

Det är vad {{PAIN}} kostar ditt bolag just nu.

Två vägar härifrån:

1. Du fortsätter som idag — kostnaden bara ökar (vi har sett 12-18% årlig ökning hos liknande bolag).
2. Vi tar 15 min och går igenom hur du tar bort kostnaden. Ingen pitch, jag visar bara vad andra gjort.

Bokar du tid här: {{CALENDAR_URL}}

— {{SIGNATUR}}
```

### Email 2 — Dag 2

**Subject:** Vad ditt resultat egentligen betyder

```
Hej {{FIRST_NAME}},

Din kalkylator gav {{CALCULATED_VALUE}} kr/år. Men det säger inte hela storyn.

Tre saker som faktiskt kostar dig:

1. {{HIDDEN_COST_1}}
2. {{HIDDEN_COST_2}}
3. {{HIDDEN_COST_3}}

När vi räknar med allt blir siffran närmare {{HIGHER_ESTIMATE}} kr.

Inte för att skrämma. Bara så du vet vad du jämför mot.

— {{SIGNATUR}}
```

### SMS — Dag 5

```
Hej {{FIRST_NAME}}, vill du gå igenom kalkylator-resultatet tillsammans? 15 min, ingen pitch. — {{SENDER}}
```

### Email 3 — Dag 7

**Subject:** Demo eller djupare resurs

```
Hej {{FIRST_NAME}},

Två alternativ beroende på var du är just nu:

1. Du är ute efter att förstå mer på egen hand → här är vår djup-guide: {{RESOURCE_URL}}

2. Du är ute efter att se hur det skulle se ut för ditt bolag → boka 15 min: {{CALENDAR_URL}}

Båda är gratis. Välj det som passar.

— {{SIGNATUR}}
```

### Email 4 — Dag 10

**Subject:** En sista grej — personlig assessment

```
Hej {{FIRST_NAME}},

Sista i den här serien.

Jag har en personlig assessment som tar 5 min — du svarar på 6 frågor och får en skräddarsydd rapport om exakt vad du ska göra härnäst, baserat på din situation.

Inte generic. Skräddarsydd.

Här: {{BOFU_MAGNET_URL}}

— {{SIGNATUR}}
```

---

## BOFU-sekvens (3 email + 1 SMS, 7 dagar)

Trigger: leadet fyllde i en BOFU assessment.

### Email 1 — Dag 0

**Subject:** Din assessment-rapport + boka 30 min

```
Hej {{FIRST_NAME}},

Här är din rapport: {{REPORT_LINK_OR_INLINE}}.

Topp-tre åtgärder för ditt bolag:

1. {{ACTION_1}}
2. {{ACTION_2}}
3. {{ACTION_3}}

Vill du gå igenom rapporten tillsammans? 30 min, jag visar exakt hur vi skulle implementera åtgärderna.

Boka: {{CALENDAR_URL}}

— {{SIGNATUR}}
```

### SMS — Dag 1

```
Såg din assessment, {{FIRST_NAME}}. Min kalender är öppen {{DATE_1}} och {{DATE_2}}. — {{SENDER}}
```

### Email 2 — Dag 3

**Subject:** Hur {{CASE_COMPANY}} körde igenom det

```
Hej {{FIRST_NAME}},

Förra året kom {{CASE_COMPANY}} till oss med exakt samma assessment-resultat som ditt.

Det vi gjorde: {{WHAT_WAS_DONE}}.

Resultatet på {{TIME_PERIOD}}: {{OUTCOME}}.

Vill du höra mer? Boka tid: {{CALENDAR_URL}}

— {{SIGNATUR}}
```

### Email 3 — Dag 7

**Subject:** Är det här rätt timing?

```
Hej {{FIRST_NAME}},

Direkt fråga:

Är det här rätt timing för dig?

- Yes → boka 30 min: {{CALENDAR_URL}}
- No → replya med "nej" så pausar jag
- Senare → replya med "senare i Q{{N}}" så hör jag av mig då

— {{SIGNATUR}}
```

---

## Variabel-tokens som måste fyllas i

Innan deployment, byt ut alla `{{TOKEN}}`:

- `{{FIRST_NAME}}` — leadets förnamn (CRM-merge-tag)
- `{{LEAD_MAGNET_TITLE}}` — titeln på lead magneten
- `{{RESULT_CATEGORY}}`, `{{CALCULATED_VALUE}}` — output från lead magnet
- `{{PAIN}}` — pain från leadets svar
- `{{CASE_COMPANY}}`, `{{CASE_OUTCOME}}` — case study som matchar leadets segment
- `{{CALENDAR_URL}}` — Calendly eller motsvarande
- `{{MOFU_MAGNET_URL}}`, `{{BOFU_MAGNET_URL}}` — länkar till nästa magnet i funnel:n
- `{{SIGNATUR}}` — avsändare (namn + företag + telefon om SMS-svar accepteras)
- `{{SENDER}}` — kort förnamn för SMS-signatur

## Pipeline-stadie-mapping

| Lead magnet-typ | Pipeline-stadie efter ifyllning | Funnel_stage-fält |
|---|---|---|
| TOFU (quiz, checklista) | Lead | TOFU |
| MOFU (ROI-kalkylator, jämförelse) | MQL | MOFU |
| BOFU (assessment, demo-request) | SQL | BOFU |
| Email-reply på sekvens | Opportunity | (oförändrad) |
| Bokat möte | Opportunity | (oförändrad) |
| Closed/won | Closed/Won | (oförändrad) |

## Compliance-checklist innan sekvensen körs

- [ ] Leadet har gett email opt-in genom lead magnet-form
- [ ] Opt-out-länk finns i alla email
- [ ] SMS har STOPP-instruktion
- [ ] GDPR — lagrar bara email/namn/företag/score, inte PII utanför det
- [ ] Sekvensen pausar om leadet replyar eller bokar möte
