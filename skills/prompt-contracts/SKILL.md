---
name: prompt-contracts
description: Sätter Definition of Done och failure conditions för en prompt — när outputen är klar och när Claude ska säga ifrån istället för att producera dålig text. Aktivera när användaren skriver "prompt contract", "sätt DoD", "Definition of Done för prompten", "när ska Claude säga ifrån", eller vill formalisera vad "klar" betyder för en återkommande uppgift. Levererar en kontrakt-sektion som kan klistras in i en RMUK-prompt eller stå själv.
---

# prompt-contracts

Quick-reference för att formulera Definition of Done och failure conditions för en prompt.

## Vad skillen gör

Hjälper användaren skriva en kontrakt-sektion som beskriver:

1. **Definition of Done** — exakt vad som måste stämma för att outputen ska godkännas
2. **Failure conditions** — när Claude ska säga ifrån och inte producera

Detta är samma idé som tester i kod — utan tester kan något "se rätt ut" men vara fel. Utan kontrakt kan en prompt-output "se rätt ut" men inte göra jobbet.

## Triggers

Aktivera när användaren skriver:

- "Prompt contract"
- "Sätt DoD"
- "Definition of Done"
- "När ska Claude säga ifrån"
- "Failure conditions för prompten"
- "Vad räknas som klar output"
- "Vad ska Claude vägra göra"

## Output-format

```markdown
## Definition of Done

Outputen är klar när alla nedan stämmer:

### Längd
- [Exakt ordantal eller intervall, t.ex. "150-200 ord per LinkedIn-post"]

### Format
- [Struktur — bullets, headers, sektioner]
- [Eventuella obligatoriska element — CTA, källhänvisning, hashtags]

### Tonalitet
- [Refererar till brand-guidelines.skill.md]
- [Specifika krav utöver brand — t.ex. "push-back i hook", "konkret exempel i mitten"]

### Struktur
- [Hook-regel: vad öppningen måste göra]
- [Body-regel: hur argumentet ska byggas]
- [Slut-regel: hur outputen ska landa]

### Innehåll
- [Vad MÅSTE finnas med — t.ex. konkret datapunkt, exempel från ICP]
- [Vad får INTE finnas — förbjudna ord, AI-tells, hype]

## Failure conditions

Säg ifrån istället för att producera om:

- [Krav 1 inte uppfyllt — t.ex. "användaren har inte angett ICP-segment"]
- [Krav 2 inte uppfyllt — t.ex. "inget konkret exempel finns att referera till"]
- [Krav 3 inte uppfyllt — t.ex. "brand voice-skill saknas och defaults inte godkända"]

I de fallen: beskriv vad som saknas och be om det. Producera inte en svagare version.
```

## Exempel-contracts

### Contract: LinkedIn-post

```markdown
## Definition of Done
- Längd: 180-220 ord
- Hook: första meningen är en konkret påstående med push-back, inte fråga
- Struktur: hook → 1 personlig observation → 1 konkret exempel → takeaway
- Tonalitet: kursens default (direkt, naturlig svenska, inga AI-tells)
- CTA: ingen explicit "kontakta mig" — låt värdet sälja

## Failure conditions
- ICP eller smärta är inte specificerad
- Användaren har inte gett en konkret observation/erfarenhet att bygga på
- Posten skulle behöva förbjudna ord (10x, unlock, transformera) för att fungera
```

### Contract: Cold email

```markdown
## Definition of Done
- Längd: max 90 ord
- Subject line: max 5 ord, ingen clickbait
- Hook: konkret detalj om mottagaren (visar att det inte är mass-mail)
- Body: en mening om smärta, en mening om vad du sett funka, en mening med fråga
- CTA: en specifik fråga som kan besvaras med ja/nej

## Failure conditions
- Mottagaren är ospecificerad (då blir det templated och fel)
- Ingen konkret detalj finns att personalisera med
- Smärtan är gissad, inte verifierad mot VoC
```

## Regler för bra kontrakt

- **Mätbart > subjektivt.** "200 ord" beats "lagom långt". "Ingen AI-tell" beats "naturlig text" (om brand-guidelines definierar AI-tells).
- **Failure conditions är inte fula.** Att säga ifrån är bättre output än att producera dåligt.
- **Kontrakt växer med erfarenhet.** När en prompt-output flopppar, lägg till en rad i Definition of Done.

## Failure conditions för själva kontrakt-skrivandet

Säg ifrån om:

- Användaren vill ha ett kontrakt utan att ha bestämt vad outputen ska göra — bestäm målet först
- Kontraktet skulle bli längre än prompten själv — sannolikt overengineering, förenkla
- Användaren ber om "kontrakt för allt" — kontrakt skrivs per uppgift, inte universellt

## References

- `prompt-template-rmuk` — kontraktet placeras i Output-format-sektionen av en RMUK-prompt
- `growth-os-philosophy` — kontrakt fungerar bara om det refererar till stabila Growth OS-filer
