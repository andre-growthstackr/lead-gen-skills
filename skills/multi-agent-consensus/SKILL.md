---
name: multi-agent-consensus
description: Kör 3 parallella sub-agents (Strateg, Pragmatiker, Devil's Advocate) som debatterar i 3 rundor tills dom konvergerar på en formulering. Använd skillen varje gång André skriver "kör multi-agent consensus", "debattera position", "kör konsensus på X", eller jobbar med brand voice, positioning statements, taglines, value props, headline-copy eller annan formulering där stake:n är hög och en ensam Claude-utkast tenderar bli generic eller AI-genererad. Skillen aktiveras också proaktivt när André håller på att skriva en final positioneringsmening i Modul 2 (L2.4 brand positioning) eller bygger `brand-guidelines.md` — vi vill INTE leverera positionering utan att tre röster bråkat först. Default på alla copy-beslut där André skulle ångra en svag formulering om 6 månader.
---

# multi-agent-consensus

Skill för att tvinga fram en bättre formulering än en ensam Claude-utkast hade producerat. Tre sub-agents med olika incentiv debatterar tills konsensus — inte tills den första som skriver något bra "vinner". Konflikten är poängen.

## Vad denna skill ÄR till för

- Brand voice-formuleringar (taglines, positioneringsmeningar, value props)
- Headline-copy med hög stake (ads, landing pages, kursnamn)
- Bygga `brand-guidelines.md` för Modul 2 — körs en gång per pelare
- Enstaka beslut där André är osäker mellan 2-3 varianter
- Naming (produkter, moduler, paket)

## Vad denna skill INTE gör

- Long-form copy (>200 ord) → använd `manus-writer` eller `marketing-content-generator`
- Tekniska beslut (arkitektur, val av verktyg) → vanlig Claude
- Research/fakta-frågor → `voc-research` eller NotebookLM
- Snabba utkast där André vet vad han vill → skippa konsensus, skriv direkt

Om André bara behöver ett utkast, inte konsensus — säg det och hoppa över skillen.

## Triggers — när skillen aktiveras

Skillen körs när André skriver något av:

- "kör multi-agent consensus [på X]"
- "debattera position[en]"
- "kör konsensus på X"
- "tre röster på X"
- "låt Strategen, Pragmatikern och Devil's Advocate ta det här"

Skillen körs också proaktivt (efter att Claude flaggat det och André sagt ok) när:

- André håller på att skriva en final positioneringsmening
- André bygger `brand-guidelines.md` (en konsensus-runda per pelare)
- André är synligt osäker mellan 2+ formuleringar och har sagt "vilken är bäst"
- Outputen ska visas publikt (ad, landing page, kursnamn)

## De tre agenterna

Detaljerade prompt-templates: `references/agent-personalities.md`. Sammanfattning här:

### Strateg

Tänker 3 års horisont. Optimerar mot **positionering, differentiering och long-term brand-equity**. Argumenterar för formuleringar som är ägbara (ingen annan i kategorin kan säga det), som tål att upprepas i 100 ads utan att slitas, och som matchar var GrowthStackr är på väg — inte var det är idag.

Failure mode: kan bli för abstrakt, för "visionär", missar säljcykel.

### Pragmatiker

Tänker säljcykel. Optimerar mot **omedelbar konvertering, klarhet och deals nästa kvartal**. Argumenterar för formuleringar som ICP omedelbart förstår, som svarar på "vad är det här?" på 3 sekunder, och som någon kan klistra in i en kall pitch utan att förklara.

Failure mode: kan bli för generic, för "feature-driven", offrar differentiering för klarhet.

### Devil's Advocate

Rättfärdigar att riva premissen. Argumenterar **mot bägge andra** när formuleringen är klyschig, AI-genererad, översatt från engelska, eller säger samma sak som varje konkurrent. Frågar: "Skulle André skämmas över att läsa det här högt på en scen?" och "Skulle vi själva tro på det här om vi såg det på en konkurrents site?"

Failure mode: kan bli förstörare utan att bygga — måste alltid föreslå alternativ, inte bara skjuta ner.

## Workflow — 3 rundor till konvergens

### Runda 1: Tre parallella utkast

Varje agent kör i parallell (sub-agent-anrop) och levererar:

- **Utkast** (1-3 varianter beroende på vad det gäller)
- **Rationale** (2-4 meningar — varför just denna formulering)
- **Vad jag offrade** (1-2 meningar — vad andra agenter kommer attackera)

Sub-agents får INTE se varandras utkast i denna runda. Parallellitet skyddar mot grupptänk.

### Runda 2: Kritik + revision

Varje agent får läsa de andra tvås utkast och måste:

- **Kritisera de andra två** (1-2 meningar per kritik — specifikt, inte "den är inte tillräckligt skarp")
- **Försvara sitt eget** mot förväntad kritik (1-2 meningar)
- **Revidera sitt eget utkast** baserat på vad som faktiskt höll i kritiken — eller behålla det och förklara varför

Devil's Advocate har speciell roll här: om alla tre utkast är generic eller AI-genererade efter Runda 1, ska Devil's Advocate riva alla tre och föreslå ett fjärde alternativ.

### Runda 3: Final syntes

En syntes-runda där agenterna måste konvergera. Format:

- **Konvergerad formulering** (1 mening eller kort copy-block)
- **Vem föreslog grundbiten** (Strateg / Pragmatiker / DA)
- **Vad varje agent gav upp** (1 mening per agent — vad dom kompromissade på)
- **Alla tre godkänner** — explicit ja från varje agent

Om dom inte kan konvergera efter Runda 3 → skillen levererar 2 finalist-formuleringar och lämnar valet till André. Detta är ett legitimt utfall — bättre än falsk konsensus.

## Output-format

Manuset levereras som markdown med exakt denna struktur:

```markdown
# Multi-agent consensus: [vad som debatteras]

**Input från André:** [vad André bad om — 1-2 meningar]
**Stake-nivå:** [Hög / Medel / Låg]
**Output-format:** [1 mening / kort copy-block / tagline + subtitle / etc.]

---

## Runda 1: Tre parallella utkast

### Strateg

**Utkast:** [formulering]

**Rationale:** [2-4 meningar]

**Offrade:** [1-2 meningar]

### Pragmatiker

**Utkast:** [formulering]

**Rationale:** [2-4 meningar]

**Offrade:** [1-2 meningar]

### Devil's Advocate

**Utkast:** [formulering ELLER "river premissen, här är fjärde alternativet"]

**Rationale:** [2-4 meningar]

**Offrade:** [1-2 meningar]

---

## Runda 2: Kritik + revision

### Strateg kritiserar

- Pragmatikerns utkast: [specifik kritik]
- Devil's Advocates utkast: [specifik kritik]

**Revision av eget utkast:** [ny formulering ELLER "behåller, för att..."]

### Pragmatiker kritiserar

- Strategens utkast: [specifik kritik]
- Devil's Advocates utkast: [specifik kritik]

**Revision av eget utkast:** [ny formulering ELLER "behåller, för att..."]

### Devil's Advocate kritiserar

- Strategens utkast: [specifik kritik]
- Pragmatikerns utkast: [specifik kritik]

**Revision av eget utkast:** [ny formulering ELLER "river båda andra, här är min slutgiltiga"]

---

## Runda 3: Final syntes

**Konvergerad formulering:**

> [Den faktiska, finala formuleringen]

**Grundbit kom från:** [Strateg / Pragmatiker / Devil's Advocate]

**Vad varje agent gav upp:**

- Strateg: [1 mening]
- Pragmatiker: [1 mening]
- Devil's Advocate: [1 mening]

**Godkännande:** Strateg ✓ / Pragmatiker ✓ / Devil's Advocate ✓

---

## Debatt-logg (kort)

[3-5 meningar om vad som faktiskt drev konvergensen. Var det Strategens insikt om kategori-positionering? Var det Pragmatikerns krav på 3-sek-klarhet? Var det DA som tvingade en omformulering? Detta är för André att lära av nästa gång.]

---

## Self-check (obligatorisk)

- [ ] Alla 3 agenter levererade utkast i Runda 1
- [ ] Ingen agent såg andras utkast innan Runda 2
- [ ] Varje kritik i Runda 2 är specifik (inte "för svag" — säger varför)
- [ ] Final formulering har minst en agents revision i sig (inte oförändrat utkast)
- [ ] Inga AI-tells i den finala formuleringen
- [ ] Inga förbjudna ord (Strategy Layer, trick-formuleringar, mental karta, revolutionerande, transformera, unlock, synergier, game-changer)
- [ ] Final formulering klarar "skulle André säga det högt"-testet
- [ ] Debatt-loggen säger något konkret (inte "alla bidrog")
```

## Voice-regler för agenterna

Agenterna är inte tre olika AI:er. Dom är tre olika incentiv inom samma kropp. Alla pratar i Andrés voice (direkt, korta meningar, konkret) — men optimerar mot olika saker.

Förbjudna ord gäller alla tre agenterna och den finala formuleringen:

- **AI-tells:** "i den här", "låt oss", "viktigt att förstå", "sammanfattningsvis", "som du kanske vet"
- **Hype:** "revolutionerande", "spelförändrande", "10x", "transformera", "unlock", "synergier", "game-changer"
- **Direktöversatt engelska:** "Strategy Layer" (säg "strategi-grunden"), "Unlock potential"
- **Krystade fraser:** "trick-formuleringar", "mental karta", "mindset-skifte"

Om en agent föreslår en formulering med något av ovanstående — Devil's Advocate har skyldighet att flagga och kräva omskrivning.

Full lista och nyans-regler: `references/agent-personalities.md`.

## Hur skillen körs tekniskt

Sub-agents anropas via Claude Code's sub-agent-funktionalitet. Tre parallella `Task`-anrop med varsin system prompt (en per agent-personlighet). Varje sub-agent får:

1. System prompt från `references/agent-personalities.md` (rätt sektion)
2. Andrés brief (vad ska formuleras + kontext)
3. För Runda 2 + 3: andra agenternas output bifogad

Skillen själv (huvud-Claude) orkestrerar rundorna — väntar på alla tre, samlar output, kör nästa runda. Skillen syntetiserar inte själv — det gör agenterna i Runda 3.

Om sub-agent-funktionalitet inte är tillgänglig: kör de tre rollerna sekventiellt i samma Claude-session med explicit role-switching ("Nu pratar Strategen. Nu pratar Pragmatikern."). Parallellitet är bättre men sekventiell version fungerar.

## Workflow — så här kör du skillen

1. **Klargör briefen.** Vad ska formuleras? För vem? I vilket format? Vad är stake:n? Om André bara säger "kör konsensus" — fråga 2-3 frågor först. Hoppa inte rakt in.

2. **Läs `references/agent-personalities.md`.** Första gången i en session. Sedan kan du köra ur huvudet.

3. **Sätt upp kontexten en gång.** Brief, ICP-snutt, brand voice-regler (om Modul 2-skillen finns), exempel på vad som INTE ska låta som det. Alla tre agenterna får samma kontext-paket.

4. **Kör Runda 1 parallellt.** Tre sub-agent-anrop. Vänta på alla tre.

5. **Kör Runda 2.** Skicka alla tre Runda 1-output till alla tre agenter. Vänta på revisions + kritik.

6. **Kör Runda 3.** Tvinga konvergens. Om inte möjligt → leverera 2 finalister + lämna valet till André.

7. **Kör self-check.** Punkt för punkt. AI-tell-sökorden grep:as.

8. **Leverera output i formatet ovan.** Som markdown direkt i chatten, eller som fil om André bett om det (typ `decisions/positioning-L2.4.md`).

## När det krånglar

- **"Alla tre agenter sa samma sak i Runda 1."** Antingen är briefen för smal (för många constraints), eller också körde du inte agenterna parallellt — dom har snott från varandra. Kör om Runda 1 med tydligare role-separation.
- **"Devil's Advocate skjuter ner allt men föreslår inget."** Devil's Advocate måste ALLTID föreslå alternativ, inte bara kritisera. Tvinga ett utkast. Om det är fortfarande tomt — Devil's Advocate har fel personlighet, läs om från `agent-personalities.md`.
- **"Konvergens i Runda 3 är falsk — alla säger 'jag accepterar' utan att ha gett upp något."** Kör en Runda 4 där varje agent måste explicit säga "Jag gav upp X för att få Y". Om dom inte kan — det finns ingen riktig konsensus, leverera 2 finalister.
- **"Slutformuleringen låter som AI."** Devil's Advocate failade sitt jobb. Kör en separat sista-pass där Devil's Advocate ensam får riva och omformulera tills det låter som André pratar.
- **"André valde inte mellan finalisterna."** Sätt deadline — "Säg vilken senast imorgon, annars går vi med Strategens." Tomgångs-konsensus är värre än beslut.

## Reference-filer

- `references/agent-personalities.md` — Detaljerade system prompts per agent. Failure modes. Exempel på god vs dålig output per roll. Läs första gången i en session.
