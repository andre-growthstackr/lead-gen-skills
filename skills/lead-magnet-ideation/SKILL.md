---
name: lead-magnet-ideation
description: Generera 10-15 lead magnet-idéer fördelat på TOFU/MOFU/BOFU för en B2B-marknadsförare, baserat på icp-summary.md + voc-rapport.md från Project. Varje idé får format (quiz/kalkylator/assessment/checklista), vilken pain den löser och vilken signal som indikerar köpfönster. Aktivera den när användaren skriver "lead magnet idéer", "ideation lead magnets", "TOFU MOFU BOFU lead magnets", "vilka lead magnets ska jag bygga", eller bara "ge mig lead magnet-förslag baserat på min ICP". Skill:en triggar även när användaren ber om "lead magnet-brainstorm" utan att specificera funnel-stadie — den ÄR funnel-fördelningen. Output är `lead-magnet-ideer.md` med tre tabeller (en per stadie) plus en kort rationale-sektion. Skill:en hoppar aldrig över ICP- och voc-läsningen — den är hela poängen.
---

# lead-magnet-ideation

Skill för att brainstorma lead magnets till en specifik B2B-ICP. En lead magnet per funnel-stadie — inte en generic "subscribe to newsletter". Idéerna kommer från pain points i `voc-rapport.md` och köpsignaler i `icp-summary.md`, inte från Claude:s allmänna kunskap.

## Vad skillen gör

- Läser `icp-summary.md` + `voc-rapport.md` från Project (output från Modul 2)
- Genererar 10-15 lead magnet-idéer fördelat över TOFU / MOFU / BOFU
- Per idé: format (quiz / kalkylator / assessment / checklista / mall / mini-tool), vilken pain den löser, vilken signal som indikerar köpfönster
- Skriver `lead-magnet-ideer.md` med tre tabeller (en per stadie) + rationale per topp-tre
- Föreslår tre topp-idéer som användaren bör bygga först (en per stadie)

## Vad skillen INTE gör

- Bygger inte själva lead magneten → använd `lead-magnet-builder-interactive`
- Skriver inte landningssida för lead magnet → använd `landing-page-builder`
- Sätter inte upp CRM-flow runt lead magnet → använd `crm-pipeline-mcp`
- Tar inte fram ICP eller VoC — det är input, inte output

Om användaren ber om något av ovanstående: hänvisa till rätt skill.

## Triggers

Aktivera när användaren skriver eller säger:

- "lead magnet idéer"
- "ideation lead magnets"
- "TOFU MOFU BOFU lead magnets"
- "ge mig 10 lead magnet-förslag"
- "vad ska jag bygga för lead magnet"
- "lead magnet-brainstorm"
- "lead magnet-ideation"
- Eller refererar till Lektionspraktik i L3.3 ("kör Ideation-prompten")

Undertrigga inte. Om användaren ens nämner "lead magnet" i samband med "idé / förslag / brainstorm / ideation" — kör skill:en.

## Input som krävs innan du börjar

1. **`icp-summary.md`** från Project-filerna (output från Modul 2 ICP-skill)
2. **`voc-rapport.md`** från Project-filerna (output från Modul 2 VoC-skill)

Om någon av dessa filer saknas — fråga användaren. Generera inte lead magnets på lös basis. Generic lead magnet-listor är värdelösa.

Om båda finns — börja direkt utan att fråga.

## Workflow

### Steg 1: Läs in ICP + VoC

Hämta båda filerna. Skanna efter:

- **Från `icp-summary.md`:** firmographics, tech stack, mätbara smärtor, köpfönster-signaler
- **Från `voc-rapport.md`:** topp-3 pain points (ordagrant språk), vad som triggar köpsamtal, vad som blockar köp

Lista vilka pains du har att jobba med. Minst 3-5 pains måste vara identifierade — annars stoppa och be om bättre VoC.

### Steg 2: Mappa pains till funnel-stadier

Varje pain har ett naturligt funnel-läge:

- **TOFU** = användaren känner inte ens igen problemet ännu. Lead magnet ska få dem att inse "okej, det här är ett problem för mig".
- **MOFU** = användaren vet att problemet finns men vet inte vad det kostar eller hur det löses. Lead magnet ska kvantifiera eller jämföra.
- **BOFU** = användaren är nära köp. Lead magnet ska ge en personlig rekommendation som pekar mot din lösning.

Sortera dina pains över de tre stadierna. Vissa pains kan ge idéer i flera stadier.

### Steg 3: Generera idéer per stadie

Mål: 4-5 idéer per stadie, totalt 10-15.

**Standard-format per stadie (default-startpunkt):**

| Stadie | Default-format | Varför |
|---|---|---|
| TOFU | Pain awareness-quiz eller checklista | Lågt friktionsmotstånd, ger användaren en aha-stund |
| MOFU | ROI-kalkylator eller jämförelse-tool | Sätter siffra på problemet, bygger urgency |
| BOFU | Personlig assessment med rekommendation | Skräddarsydd output → pekar direkt mot köp |

Du får avvika från default-formaten om en pain naturligt passar bättre i ett annat format (t.ex. en mall, ett mini-tool, en swipe file). Men minst en idé per stadie ska vara i default-formatet.

### Steg 4: Per idé — fyll i tre kolumner

För varje idé, fyll i:

1. **Format** — exakt verktygsformat (quiz / ROI-kalkylator / assessment / checklista / mall / mini-tool / swipe file)
2. **Pain den löser** — vilken specifik pain från VoC eller ICP. Citera språket från VoC-rapporten om möjligt.
3. **Signal som indikerar köpfönster** — vad säger det att leadet använt denna lead magnet? T.ex. "score över 70 = mogen för MOFU-sekvens", "valt 'mer än 100 leads/mån' = SQL-kandidat".

### Steg 5: Skriv `lead-magnet-ideer.md`

Output-format (se nedan). Tre tabeller (en per stadie) + rationale-sektion + topp-tre-rekommendation.

### Steg 6: Föreslå topp-tre

Välj en idé per stadie som täcker:

1. Den starkaste pain från VoC
2. Den högsta troliga conversion (BOFU > MOFU > TOFU för conversion, men volym är omvänd)
3. Lägst byggkomplexitet relativt impact

Markera dessa tre tydligt i output:en. Användaren kommer bygga dem i `lead-magnet-builder-interactive` direkt efter.

## Output-format

Filnamn: `lead-magnet-ideer.md`

Struktur:

```markdown
# Lead magnet-idéer — [Företagsnamn / ICP-namn]

**Källor:** icp-summary.md + voc-rapport.md  
**Genererad:** [datum]  
**Totalt antal idéer:** [10-15]

---

## TOFU — Pain awareness (4-5 idéer)

| # | Format | Idé (titel) | Pain den löser | Signal vid ifyllning |
|---|---|---|---|---|
| T1 | Pain awareness-quiz | [Titel] | [Specifik pain från VoC] | [Signal] |
| T2 | Checklista | [Titel] | ... | ... |
| ... | ... | ... | ... | ... |

---

## MOFU — Kvantifiera + jämför (4-5 idéer)

| # | Format | Idé (titel) | Pain den löser | Signal vid ifyllning |
|---|---|---|---|---|
| M1 | ROI-kalkylator | [Titel] | ... | ... |
| ... | ... | ... | ... | ... |

---

## BOFU — Personlig rekommendation (4-5 idéer)

| # | Format | Idé (titel) | Pain den löser | Signal vid ifyllning |
|---|---|---|---|---|
| B1 | Assessment med rekommendation | [Titel] | ... | ... |
| ... | ... | ... | ... | ... |

---

## Topp-tre att bygga först

1. **[T-id eller M-id eller B-id]: [Titel]** — [En mening om varför just denna först]
2. **[id]: [Titel]** — [En mening]
3. **[id]: [Titel]** — [En mening]

---

## Rationale per topp-tre

### [T-id]: [Titel]
- **Pain (från VoC):** [citat eller paraphrase]
- **Varför detta format:** [1-2 meningar]
- **Förväntad signal:** [vad det säger när leadet använt den]
- **Nästa steg när leadet kommit ut:** [vilken sekvens som ska triggas]

### [M-id]: [Titel]
[samma struktur]

### [B-id]: [Titel]
[samma struktur]
```

## Failure conditions

Lever inte filen om något av detta är sant:

- **Färre än 10 idéer totalt** — gå tillbaka och brainstorma fler
- **Mindre än 3 idéer i något stadie** — fyll på, fördelningen måste vara balanserad
- **En idé har ingen specifik pain från VoC** — generic ideation är dödfött, ta bort idén
- **Två idéer i samma stadie är samma format med samma pain** — duplikat, slå ihop eller ta bort en
- **Topp-tre-listan saknar rationale** — fyll i innan leverans
- **icp-summary.md eller voc-rapport.md saknas i Project** — stoppa, fråga användaren

## Anti-patterns att undvika

- "10 bästa lead magnets för B2B" — generic, du har inte använt ICP:n
- "Whitepaper om [bransch-trend]" — PDF/whitepaper är inte interaktivt, hör inte hemma som default-output
- "Newsletter signup" — det är inte en lead magnet, det är en RSS-feed
- "E-bok om [ämne]" — samma problem som whitepaper, döda 2015-format
- Lead magnet utan tydlig signal — om du inte vet vad ifyllningen säger om leadet är magnet:en värdelös för segmentering

## References

- `references/lead-magnet-format-bibliotek.md` — utvidgad lista med 30+ format per stadie, exempel-titlar, byggkomplexitet
- `references/signal-mapping.md` — hur du tolkar svar från olika lead magnet-format som köpsignaler (för CRM-routing i L3.4)

## Källkrav

Varje idé:

- **Pain ska citeras eller paraphraseras från `voc-rapport.md`** — markera om citat, lägg in i citat-tecken
- **Signal ska kunna mappas mot ett fält i CRM** — om signalen är "leadet känns intresserat" är den värdelös, gör om

Hitta inte på pains som inte finns i VoC. Om en pain inte är verifierad — fråga användaren innan du bygger en idé runt den.
