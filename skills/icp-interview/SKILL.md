---
name: icp-interview
description: Kör en interview-driven ICP-research i 12 frågor (en i taget — reverse prompting) för en svensk B2B-marknadsförare, scrapar sedan kundens hemsida via Firecrawl och genererar ett komplett `icp-spec.md` med fyra sektioner — Firmographics, Tech Stack, Mätbar Smärta, Köpfönster — plus TAM/SAM-räkning och dagligt lead-mål. Använd den varje gång användaren skriver "kör ICP-interview", "bygg min ICP", "ICP-research", eller hänvisar till Modul 2 lektion L2.1 i Claude för leadsgenerering-kursen. Använd den även om användaren bara skriver "vem är min idealkund" — denna skill ÄR processen för att svara på det. Skippa inte frågorna och hitta inte på svar — interviewn är grunden för allt downstream-arbete.
---

# icp-interview

Skill för att bygga en empirisk ICP via strukturerad interview. Lektion L2.1 i Claude för leadsgenerering. Output är en fil användaren faktiskt kan använda för att rikta sin pipeline — inte en abstrakt persona-PDF.

## Vad denna skill gör

- Ställer 12 frågor i fast ordning, en åt gången (reverse prompting)
- Scrapar användarens hemsida via Firecrawl-MCP efter interviewn
- Bygger `icp-spec.md` med fyra sektioner + TAM/SAM-räkning + dagligt lead-mål
- Flaggar svar som är för vaga och ber om omformulering innan vi går vidare

## Vad denna skill INTE gör

- Voice of Customer-research → använd `voc-research`
- Brand voice → använd `brand-voice-interview`
- Skapar inte personas med påhittade namn och ansiktsbilder

## Triggers

Aktivera skillen när användaren skriver något av följande:

- "kör ICP-interview"
- "bygg min ICP"
- "ICP-research"
- "vem är min idealkund"
- "starta L2.1"
- "vi behöver göra ICP:n"

Aktivera även om användaren bara beskriver problemet ("jag vet inte vem jag säljer till") och kontexten är leadsgenerering eller B2B-marknadsföring.

## Workflow

### Steg 1 — Initiera interviewn

Säg kort vad som händer:

> "Vi kör en ICP-interview. 12 frågor, en i taget. Skriv så ärligt och konkret som möjligt — vaga svar ger vag ICP. När vi är klara scrapar jag hemsidan och bygger ditt icp-spec.md."

Be om hemsidans URL direkt så att den finns när det är dags att scrapa.

### Steg 2 — Ställ frågorna i ordning

En fråga per tur. Vänta på svar. Vid vagt svar — be om konkretisering innan nästa fråga.

**Fråga 1:** Vad säljer ditt företag? Beskriv produkten/tjänsten i 1-2 meningar.

**Fråga 2:** Vilka är dina 3-5 senaste closed/won-kunder? Namn på företag.

**Fråga 3:** För varje av de kunderna — vad var den utlösande händelsen som triggade köpet? (T.ex. ny CRO, växte ur Mailchimp, förlorade en viktig kund, fick CMO-roll)

**Fråga 4:** Hur lång var säljcykeln från första kontakt till closed/won? (Veckor/månader)

**Fråga 5:** Vad var deras tekniska stack innan köp? (CRM, marketing automation, analytics, etc.)

**Fråga 6:** Vilken funktion/roll var beslutsfattaren? (CMO, Head of Growth, Marketing Manager, Founder, etc.)

**Fråga 7:** Vad var den mätbara smärtan i kronor/månad? (Förlorad pipeline, slöseri på ads, timmar som inte fakturerades, etc.)

**Fråga 8:** Vilka kanaler använder dina kunder för research innan köp? (LinkedIn, podcasts, Slack-communities, branschpublikationer, Google, etc.)

**Fråga 9:** Vad försökte de innan de hittade er? (Andra verktyg, byråer, in-house-försök som misslyckades)

**Fråga 10:** Vad är genomsnittlig deal-size? (ACV eller månadlig ARR)

**Fråga 11:** Vilken intent-signal indikerar köpfönster? (Anställde någon, bytte verktyg, fick funding, publicerade jobbannons för X, etc.)

**Fråga 12:** Vilka 20-50 företag liknar dina 3-5 closed/won? Detta blir TAM-basen.

### Steg 3 — Vagt-svar-detektion

Om ett svar saknar specificitet — be om omformulering INNAN du går vidare. Exempel:

- "B2B-företag i Norden" → för brett, be om branscher + storlek
- "De behövde bättre marknadsföring" → vad var den faktiska smärtan i kr eller timmar?
- "Vi tar ofta 6-månaders deals" → är det ACV eller månadsvärde?
- "De använde HubSpot" → bara CRM eller även marketing hub? Vad mer i stacken?

Använd push-back: "Det här är för luddigt för att bygga ICP på. Ge mig ett exempel — vilken specifik smärta hos vilket specifikt företag?"

### Steg 4 — Hemside-scraping

Efter fråga 12 — scrapa URL:en från Fråga 1/intro via Firecrawl-MCP:

- `mcp__firecrawl__firecrawl_scrape` på huvudsidan
- Hämta `/om-oss`, `/customers`, `/case-studies`, `/pricing` om de finns
- Använd `mcp__firecrawl__firecrawl_map` för att lista sidor först

Extrahera:
- Produkt-positionering (hur de pitchar sig själva)
- Case studies / kundlogotyper (verifiera mot Fråga 2)
- Pricing-tier (verifiera mot Fråga 10)
- Tech stack-hints (integrationer som listas)

Om Firecrawl inte är tillgänglig — flagga det och fortsätt utan, men markera i icp-spec att hemside-validering inte är gjord.

### Steg 5 — TAM/SAM-räkning

Använd Fråga 12-basen (20-50 företag som liknar de closed/won):

- **TAM:** Totalt antal företag i Sverige/Norden/EU som matchar firmographics-filtret (anställda, omsättning, bransch). Källor: SCB, allabolag.se, Crunchbase. Ange källan.
- **SAM:** Subset av TAM som matchar tech stack + köpfönster-signaler. Räkna konservativt — typiskt 10-30% av TAM.
- **Dagligt lead-mål:** Givet deal-size, säljcykel och pipeline-coverage-ratio (default 3x), räkna ut hur många nya leads/dag som behövs.

Räkne-exempel: ACV 120 000 kr, mål 1,2 MSEK ARR/år = 10 deals = 30 leads i pipeline = 6 nya leads/månad = 0,3/arbetsdag. Visa räkningen.

### Steg 6 — Generera icp-spec.md

Spara filen som `icp-spec.md` i användarens nuvarande working directory eller där användaren bett om. Strukturen finns i Output-format nedan.

## Output-format

```markdown
# ICP-spec: [Företagsnamn]

**Datum:** [YYYY-MM-DD]
**Källa:** Interview + hemside-scraping ([URL])
**Status:** Utkast v1 — validera mot 3-5 nya prospects innan låsning

---

## 1. Firmographics

| Filter | Värde |
|---|---|
| Land/region | [Sverige / Norden / EU] |
| Anställda | [50-200, t.ex.] |
| Omsättning | [10-100 MSEK, t.ex.] |
| Bransch | [SaaS, byråer, tillverkning, etc.] |
| Tillväxtfas | [Series A, bootstrapped >5 år, etc.] |

**Beslutsfattare:**
- Primär: [roll, t.ex. CMO]
- Sekundär: [roll, t.ex. Head of Growth]
- Influencer: [t.ex. CRO eller VD]

---

## 2. Tech stack

**Typisk pre-köp-stack:**
- CRM: [HubSpot / Pipedrive / Salesforce]
- Marketing automation: [Mailchimp / ActiveCampaign / HubSpot]
- Analytics: [GA4 / Mixpanel / Plausible]
- Ads: [Google Ads, Meta, LinkedIn]
- Övrigt: [Notion, Slack, etc.]

**Vad de byter från / kompletterar:**
- [T.ex. "Outgrowing Mailchimp, behöver lead scoring"]

---

## 3. Mätbar smärta

**Den utlösande händelsen:**
- [T.ex. "Ny CMO som mätaas på SQL/månad", eller "Förlorat 2 stora kunder, behöver fylla pipeline"]

**Smärta i kr/månad:**
- [T.ex. "Förlorar ca 200 000 kr/månad i pipeline pga för få SQL"]
- [T.ex. "Slösar 80 000 kr/månad på ads utan attribution"]

**Vad de försökte innan:**
- [T.ex. "Anlitade byrå för 60 k/månad — fick traffic men inga leads"]
- [T.ex. "In-house växte ur, junior marketer kunde inte sätta systemet"]

---

## 4. Köpfönster — intent-signaler

**Trigger-signaler att leta efter:**
- [T.ex. "Anställer Head of Growth"]
- [T.ex. "Byter från Mailchimp till HubSpot"]
- [T.ex. "Publicerar jobbannons med 'marketing automation' i krav"]
- [T.ex. "Fick Series A senaste 12 mån"]

**Kanaler där de finns:**
- [T.ex. "LinkedIn — följer Lenny Rachitsky, Reforge, ChartMogul"]
- [T.ex. "Slack: SaaStr, RevOps Co-op"]
- [T.ex. "Podcasts: B2B Growth, Marketing Architects"]

---

## 5. TAM / SAM / Dagligt mål

**TAM:**
- [N företag] i [region] som matchar firmographics
- Källa: [SCB / allabolag.se / Crunchbase + datum]

**SAM:**
- [N företag] som även matchar tech stack + visat köpfönster-signal senaste 12 mån
- Antagande: [X%] av TAM, baserat på [grund]

**Dagligt lead-mål:**
- ARR-mål: [X MSEK]
- ACV: [Y kr]
- Antal deals/år: [N]
- Pipeline coverage: [3x default]
- Nya leads/månad: [N]
- **Nya leads/arbetsdag: [N]**

---

## 6. Validering & nästa steg

- [ ] Validera ICP mot 3-5 nya prospects (inte de closed/won som ingick i interviewn)
- [ ] Kör `voc-research` för att hitta exakta ord och invändningar
- [ ] Bygg list i Apollo/Cognism med firmographics + intent-filter
- [ ] Spåra första 20 outbound — mäter reply rate mot baseline
```

## Failure conditions

Säg ifrån istället för att generera på lös basis när:

- **Fråga 2 är tom eller hypotetisk.** Inga riktiga closed/won = ingen ICP. Be användaren komma tillbaka när de har 3-5 riktiga affärer, eller kör en pre-revenue-variant där vi använder marknadsantaganden (markera tydligt som hypotes).
- **Fråga 7 är ospecificerad efter två omformuleringsförsök.** Smärtan måste vara i kr eller timmar — annars bygger vi positioneringen på känsla, inte data.
- **Fråga 12 är "alla SaaS-bolag i Sverige" eller liknande.** För brett. Be om konkreta företagsnamn — minst 10 stycken.
- **Hemsidan är otillgänglig och Firecrawl returnerar fel.** Fortsätt utan scraping men flagga att hemside-validering saknas.

Hitta inte på siffror för TAM/SAM. Om användaren inte vet — be dem öppna allabolag.se eller motsvarande och köra filtret manuellt, eller markera TAM som "att räkna".

## References

- `references/tam-sam-räkning.md` — Konkreta räkneexempel + svenska datakällor (SCB, allabolag, Crunchbase)
- `references/intent-signaler.md` — Bibliotek av intent-signaler per bransch + var de hittas
