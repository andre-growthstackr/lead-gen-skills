---
name: brand-voice-interview
description: Kör en interview-driven brand voice-research i 8 frågor (en i taget) plus hemside-scraping via Firecrawl. Genererar både ett komplett `brand-guidelines.md` med fem sektioner (Voice, Visual identity, Positioning, Differentiation, Negative rules) och en kortfattad `brand-guidelines.skill.md` som blir återanvändbar i alla framtida copy-jobb. Använd den varje gång användaren skriver "brand voice-interview", "bygg brand voice", "starta L1.3", eller hänvisar till Modul 1 lektion L1.3 i Claude för leadsgenerering-kursen. Använd den även när användaren skriver "jag behöver en brand voice" eller "min copy låter inte som mig" — denna skill ÄR processen.
---

# brand-voice-interview

Skill för att bygga ett brand voice-dokument via strukturerad interview + hemside-scraping. Lektion L1.3 i Claude för leadsgenerering. Output är två filer — ett komplett brand guidelines-dokument plus en kortfattad skill som andra prompts plockar från i framtida copy-jobb.

## Vad denna skill gör

- Ställer 8 frågor i fast ordning, en åt gången
- Scrapar användarens hemsida via Firecrawl (eller två-tre konkurrent-sidor för kontrast)
- Genererar `brand-guidelines.md` med fem sektioner
- Genererar `brand-guidelines.skill.md` som kodifierar voice för återanvändning

## Vad denna skill INTE gör

- ICP-research → använd `icp-builder`
- VoC-mining → använd `voc-research`
- Skriver inte färdig copy → använd brand-guidelines.skill.md som input till copy-jobb
- Skapar inte logotyp, färgpalett från noll, eller designsystem — bara extraherar från hemsidan om det finns

## Triggers

Aktivera skillen när användaren skriver något av följande:

- "brand voice-interview"
- "bygg brand voice"
- "starta L1.3"
- "jag behöver en brand voice"
- "min copy låter inte som mig"
- "vi har ingen brand voice-dokumentation"

Aktivera även när användaren ber om copy och det är uppenbart att de saknar brand guidelines.

## Workflow

### Steg 1 — Initiera interviewn

Säg kort vad som händer:

> "Vi kör en brand voice-interview. 8 frågor, en i taget. Ärliga svar > polerade svar. Sedan scrapar jag hemsidan och bygger två filer — guidelines och en återanvändbar skill."

### Steg 2 — Ställ frågorna i ordning

En fråga per tur. Vänta på svar. Be om konkretisering vid vagt svar innan nästa fråga.

**Fråga 1:** Vilket företag pratar vi om? (Namn)

**Fråga 2:** Vad är hemsidans URL?

**Fråga 3:** Beskriv tonen i en mening. (Är ni formella, vassa, personliga, akademiska, irriterade, etc.?)

**Fråga 4:** Vilka 3 ord beskriver hur ni låter när det är på topp?

**Fråga 5:** Vilka 3 ord beskriver hur ni absolut INTE vill låta?

**Fråga 6:** Vilken konkurrent låter mest fel? Varför? (Konkret — namnge dem och säg vad de gör som ni hatar)

**Fråga 7:** Skicka mig 2-3 textexempel från er bästa befintliga copy. (Paste in det, eller länka till sida)

**Fråga 8:** Skicka mig 2-3 textexempel från typisk industristandard ni vill bryta mot. (T.ex. "varannan SaaS-hemsida säger 'transform your business' — vi vill aldrig låta så")

### Steg 3 — Vagt-svar-detektion

Vagt svar på Fråga 3-6 = oanvändbar voice-output. Push tillbaka konkret:

- "Vi är professionella" → vad betyder det? Akademiskt? Korrekt? Försiktigt? Be om ett ord till.
- "Vi vill inte vara säljiga" → vad är säljigt för er? CTA? Hype-adjektiv? Caps?
- "Konkurrenten X låter dåligt" → vad i deras copy? Ge mig en mening de skriver som ni aldrig skulle säga.
- "Vi vill låta som [känd företag]" → vad specifikt i deras voice? Korta meningar? Self-deprecating humor? Tekniska detaljer?

### Steg 4 — Hemside-scraping

Efter Fråga 8 — scrapa URL:en från Fråga 2 via Firecrawl-MCP:

- `mcp__firecrawl__firecrawl_map` för att lista sidor
- `mcp__firecrawl__firecrawl_scrape` på:
  - Startsidan
  - `/om-oss` eller `/about`
  - `/produkter` eller `/services`
  - 1-2 case studies om de finns
  - Blog-startsidan + senaste 2-3 inläggen

Extrahera:
- **Recurring fraser** — vad upprepas? (Bra eller dåligt — du noterar bara mönster)
- **Mening-längd** — korta? Långa? Mix?
- **CTA-stil** — "Boka demo" vs "Snacka med oss" vs "Kom igång nu"
- **Branded vokabulär** — egna termer/produktnamn
- **Visuella signaler** — färger (HEX om syns i CSS), typografi (font-family i HTML)

Om Firecrawl är otillgänglig — flagga, fortsätt utan visual-sektionen.

### Steg 5 — Konkurrent-kontrast (valfritt men starkt)

Om Fråga 6 namnger konkurrent — scrapa deras startsida för kontrast. Hjälper "Negative rules"-sektionen att bli konkret. Behöver inte mer än startsida + om-oss.

### Steg 6 — Generera `brand-guidelines.md`

Spara i nuvarande working directory. Strukturen finns i Output-format nedan.

### Steg 7 — Generera `brand-guidelines.skill.md`

Detta är en SEPARAT SKILL-fil. Den är kortare än `brand-guidelines.md` — mål: ≤200 rader. Framtida copy-jobb läser den för att veta voice utan att läsa hela guidelines.

Strukturen finns i Output-format nedan.

## Output-format

### Fil 1: `brand-guidelines.md`

```markdown
# Brand guidelines: [Företagsnamn]

**Datum:** [YYYY-MM-DD]
**Källa:** Interview + hemside-scraping ([URL])
**Status:** Utkast v1 — validera med André eller team innan låsning

---

## 1. Voice — hur ni låter

**Tonen i en mening:** [från Fråga 3, omformulerad om vagt]

**Vi låter:**
- [Adjektiv 1 från Fråga 4] — [exempel-mening som visar det]
- [Adjektiv 2] — [exempel]
- [Adjektiv 3] — [exempel]

**Vi låter aldrig:**
- [Adjektiv 1 från Fråga 5] — [exempel på vad det betyder konkret]
- [Adjektiv 2] — [exempel]
- [Adjektiv 3] — [exempel]

**Mening-rytm:** [Vad scrape:n visade — kort? Mix? Långa?]

**Sätt att starta meningar (mönster vi gillar):**
- [Mönster 1 från befintlig bra copy, Fråga 7]
- [Mönster 2]
- [Mönster 3]

---

## 2. Visual identity

Om hemside-scrape lyckades:

**Primära färger:**
- [HEX-kod] — [namn om uppenbart, t.ex. "Brand orange"]
- [HEX] — [namn]

**Typografi:**
- Headline: [font-family från CSS]
- Body: [font-family]

**Bildstil:** [Vad scrape:n visade — produktbilder? Foto av människor? Illustration? Inga bilder?]

Om scrape:n inte gav visual data — markera "Att dokumentera separat" och hoppa till sektion 3.

---

## 3. Positioning statement

Baserat på hemsidans copy + interview-svaren:

**Vad ni säljer:** [En mening]

**Till vem:** [ICP — kort form, hänvisa till `icp-summary.md` om det finns]

**Vad gör er annorlunda:** [Det specifika de gör som inte konkurrenter gör — om Fråga 6 + 7 + 8 svaren ger underlag]

**Bevis:** [Case studies, siffror, kundlogotyper från hemsidan]

---

## 4. Differentiation — vad ni INTE är

(Detta är det viktiga steget — utan det blir voice-dokumentet generiskt.)

**Vi är inte [X] (som konkurrent A):**
- De [konkret beteende från Fråga 6]
- Vi [motsatt beteende]

**Vi är inte [Y] (typisk industristandard från Fråga 8):**
- Industristandard: "[ordagrann fras industrin använder, från Fråga 8]"
- Vi: "[hur vi skulle säga det istället]"

---

## 5. Negative rules — vad vi aldrig säger

Lista konkreta ord, fraser och formuleringar att alltid undvika.

**Ord vi aldrig använder:**
- [Ord 1 — t.ex. "transformera"]
- [Ord 2 — t.ex. "synergier"]
- [Ord 3]
- ...

**Fraser vi aldrig skriver:**
- [Fras 1 — t.ex. "Ta din [X] till nästa nivå"]
- [Fras 2]
- ...

**Strukturella regler:**
- [Regel 1 — t.ex. "Aldrig CTA i caps"]
- [Regel 2 — t.ex. "Aldrig meningar längre än 25 ord i headlines"]
- ...

**Tonalitet vi undviker:**
- [Regel 1 — t.ex. "Aldrig inkluderande 'vi alla vet'-formuleringar"]
- [Regel 2]
```

### Fil 2: `brand-guidelines.skill.md`

Kortfattad återanvändbar Skill för copy-jobb. Max 200 rader.

```markdown
---
name: brand-guidelines
description: Brand voice-skill för [företagsnamn]. Använd den i alla copy-jobb (LinkedIn, ads, email, landningssidor) för att hålla voice konsekvent. Hämtar tone, do/don't-listor och negativa regler härifrån — hitta inte på voice från noll. Hänvisa till `brand-guidelines.md` för full kontext.
---

# brand-guidelines: [Företagsnamn]

Voice-regler för copy. Kortfattad version av `brand-guidelines.md` (datum [YYYY-MM-DD]).

## Voice i en mening

[Från sektion 1 i brand-guidelines.md]

## Vi låter (do)

- [Adjektiv 1] — [konkret beteende]
- [Adjektiv 2] — [beteende]
- [Adjektiv 3] — [beteende]

## Vi låter aldrig (don't)

- [Adjektiv 1] — [konkret undvika]
- [Adjektiv 2]
- [Adjektiv 3]

## Mening-rytm

[Kort regel — t.ex. "Korta meningar dominerar. Mix in långa för rytm. Aldrig 3 långa i rad."]

## Ord att undvika

- [Lista — max 15 ord, de viktigaste]

## Fraser att undvika

- [Lista — max 10 fraser]

## CTA-stil

- Föredragna formuleringar: ["X", "Y", "Z"]
- Undvik: ["Bli kund nu", "Ta kontroll över din pipeline", etc.]

## Hur denna skill används

I copy-prompts — referera direkt: "Skriv [format] enligt brand-guidelines-skillen. Voice ska följa do/don't-listorna ovan. Vid tveksamhet — använd kortare mening + naturligare formulering."

För full kontext (positioning, differentiation, visual) — läs `brand-guidelines.md`.
```

## Failure conditions

Säg ifrån istället för att leverera på lös basis när:

- **Fråga 7 är tom.** Utan befintlig bra copy att referera till blir voice-output gissning. Be användaren paste:a 2-3 exempel — även gamla.
- **Fråga 4 och 5 har samma typ av ord.** Om svaren är "professionell, smart, kunnig" vs "oprofessionell, dum, okunnig" — det är inte differentiation. Be om mer specifika ord (t.ex. "torr humor", "låter som en pojkvän som råder dig", "irriterande ärlig").
- **Hemsidan har 0 publicerad copy.** Då har ni ingen voice att extrahera, bara att skapa. Säg "Det här blir en voice-design-session, inte en voice-extraction-session. Vill du fortsätta?" och låt användaren bestämma.
- **Användaren ber dig härma en känd brand (Apple, Stripe, etc.).** Du kan ta inspiration, men brand voice ska komma från deras egna mönster — inte från en annan brand. Säg "Vi kan låna mönster från Stripe, men din voice ska byggas på din egen copy. Vad i din egen text fungerar redan?"

## References

- `references/voice-extraction-tips.md` — Hur du läser ut voice-mönster ur scrape:d copy (mening-rytm, vokabulär, CTA-stil)
- `references/vanliga-voice-fällor.md` — Tio fällor svenska B2B-företag faller i (jargong, översatt engelska, falsk personlighet)
