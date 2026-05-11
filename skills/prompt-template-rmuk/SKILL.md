---
name: prompt-template-rmuk
description: Återanvändbar prompt-mall med fyra sektioner — Roll, Mål, Uppgift, Kontext — plus Output-format. Aktivera när användaren skriver "skapa prompt", "ge mig en prompt-mall", "RMUK-prompt", "strukturera prompten", "bygg en prompt för X", eller behöver formalisera en ad-hoc-instruktion till något återanvändbart. Skillen levererar mallen ifylld om uppgiften är specificerad, eller tom mall med ledtext om användaren bara vill ha skelettet.
---

# prompt-template-rmuk

Quick-reference för RMUK-prompt-mallen som används genom hela kursen.

## Vad skillen gör

Levererar en RMUK-prompt i markdown — antingen tom mall som användaren fyller i, eller ifylld med konkret innehåll baserat på vad användaren beskrivit.

RMUK = Roll, Mål, Uppgift, Kontext. Output-format är en femte sektion som alltid finns med.

## Triggers

Aktivera när användaren skriver:

- "Skapa prompt för X"
- "Ge mig en prompt-mall"
- "RMUK-prompt"
- "Strukturera prompten"
- "Bygg en prompt"
- "Skriv om den här prompten med RMUK"
- "Formalisera den här instruktionen"

## Workflow

1. Läs av om användaren har gett konkret innehåll eller bara vill ha skelettet
2. Om konkret: fyll i alla sektioner med exakt det användaren beskrivit. Lägg inte till spekulativ kontext.
3. Om skelett: leverera tom mall med ledtext i varje sektion
4. Påminn om Growth OS-filer i Kontext-sektionen om relevanta filer finns (icp-spec.md, brand-guidelines.skill.md osv)

## Output-format

```markdown
# Roll

Du är [konkret roll — t.ex. "en B2B-content-strateg med 10 års erfarenhet av SaaS-marknadsföring"]. Du är inte en generisk AI-assistent — du tänker som någon som har gjort det här hundra gånger.

# Mål

[Vad ska outputen åstadkomma i affären? Inte "skriv en bra text" utan "öka click-through på cold email till ICP-segment X"]

# Uppgift

[Konkret leverabel. Vad ska Claude producera, i vilken form, i vilken längd?]

# Kontext

Referera till Growth OS-filer:
- ICP: se `icp-spec.md`
- Brand voice: se `brand-guidelines.skill.md`
- Positionering: se `positionering.md`
- [Andra filer relevanta för uppgiften]

Övrig kontext:
- [Mottagare/segment]
- [Tonalitet om annat än standard]
- [Restriktioner — vad får inte sägas]
- [Tidigare exempel som funkat]

# Output-format

- [Exakt struktur — sektioner, ordlängd, format]
- [Eventuella headers/bullets/format-regler]
- [Failure conditions: när säga ifrån istället för att producera dålig output]
```

## Regler för bra RMUK-promptar

- **Roll** är inte en jobbtitel utan ett perspektiv. "Senior content-strateg" beats "marketingexpert".
- **Mål** är affärsmål, inte uppgift. Skilj på vad outputen ska åstadkomma vs vad den ska vara.
- **Uppgift** är konkret. "Skriv 3 LinkedIn-poster, 150-200 ord var, hooks som öppnar med smärta" — inte "skriv social media content".
- **Kontext** är där Growth OS-filerna kopplas in. Refererar du inte till dem ärver Claude ingen verksamhetskunskap.
- **Output-format** stänger gapet mellan "det är okej" och "det är användbart". Specificera struktur, längd, format.

## Failure conditions

Säg ifrån om:

- Användaren ger för vag uppgift för att skriva en meningsfull RMUK ("hjälp mig med marketing") — be om konkret leverabel först
- Användaren vill skippa Kontext-sektionen — den är inte valfri. Utan Growth OS-referenser blir prompten ad-hoc, inte system.
- Användaren ber om RMUK för en uppgift där en enkel prompt räcker (en-rads-fråga, sökning) — RMUK är overhead för engångssaker

## References

- `growth-os-philosophy` — varför Kontext-sektionen alltid refererar till filer
- `prompt-contracts` — Output-format-sektionen kan utökas med en full Definition of Done för komplexa uppgifter
- `reverse-prompting` — om uppgiften är vag, kör reverse prompting först innan du bygger RMUK
