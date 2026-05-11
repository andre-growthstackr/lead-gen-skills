---
name: hyperframe-prompts
description: Cheatsheet för Hyperframe — motion graphics som genereras som SVG/CSS/Lottie-kod istället för renderad video. Aktivera när användaren skriver "Hyperframe", "motion graphics som kod", "SVG-animation", "Lottie-animation", "animera den här grafiken", eller vill ha lättviktig motion graphic till en video som ska bakas in i Tella/CapCut-edit. Levererar prompt-template som ärver brand-guidelines.skill.md (typografi, färger, rytm).
---

# hyperframe-prompts

Quick-reference för Hyperframe motion graphics-promptar.

## Vad skillen gör

Skriver promptar till Hyperframe som genererar motion graphics som kod (SVG + CSS animation, eller Lottie JSON). Output bakas sedan in i video-edit (Tella, CapCut, Premiere) som ett lager.

## Triggers

Aktivera när användaren skriver:

- "Hyperframe"
- "Motion graphics som kod"
- "SVG-animation"
- "Lottie-animation"
- "Animera den här grafiken"
- "Animera typografi"
- "Bake into video"

## Varför kod istället för rendered video

- **Vektor-skalbar** — funkar på 4K utan extra rendering
- **Liten filstorlek** — KB istället för MB
- **Anpassningsbar** — färger/text kan ändras utan re-rendering
- **Versionsbar** — kod kan committas, granskas, återanvändas

Trade-off: complex 3D-scener fungerar inte. För det, använd `higgsfield-prompts`.

## Prompt-template för Hyperframe

```
Type: [motion graphics typ — text-reveal, icon-build, chart-grow, transition-wipe]
Content: [det som ska animeras — text, ikon, datapunkt, etc]
Animation:
  - Sequence: [vad händer först, sedan, sist]
  - Timing: [duration per element, total length]
  - Easing: [linear, ease-out, spring]
Style:
  - Typography: [från brand-guidelines.skill.md om finns]
  - Colors: [hex-koder från brand-guidelines]
  - Stroke/fill: [tjocklek, transparens]
Output format: [SVG inline, Lottie JSON, eller CSS animation]
```

## Workflow: filma rått → Hyperframe → bake into video

1. Spela in talking-head eller skärmdelning rått i Tella
2. Identifiera moment som behöver motion graphic (datapunkt, transition, betoning)
3. Skriv Hyperframe-prompt per moment
4. Generera kod, exportera till format som video-editorn stödjer (Lottie eller SVG-sekvens)
5. Importera i Tella/CapCut, lägg som overlay-lager
6. Justera timing i edit

## Exempel per use-case

### Text-reveal (betoning på en mening)

```
Type: text-reveal
Content: "95% av AI-pilots ger noll ROI"
Animation:
  - Sequence: text fadear in från vänster, word-by-word
  - Timing: 0.15s per ord, total 1.2s
  - Easing: ease-out-cubic
Style:
  - Typography: brand-headline-font från brand-guidelines.skill.md, weight 600
  - Colors: primary brand color på "95%", default text på resten
  - Stroke/fill: solid fill, ingen stroke
Output format: SVG inline med CSS animation
```

### Icon-build (visa en process växa fram)

```
Type: icon-build
Content: Funnel-ikon med fyra stages — Lead, MQL, SQL, Customer
Animation:
  - Sequence: top stage ritar sig själv, sedan nästa, sedan nästa, sedan sista
  - Timing: 0.4s per stage, 0.1s paus mellan
  - Easing: spring
Style:
  - Typography: brand-mono-font för stage-labels
  - Colors: accent-color för aktiv stage, muted för väntande
  - Stroke/fill: 2px stroke, no fill
Output format: Lottie JSON
```

### Chart-grow (datapunkt växer fram)

```
Type: chart-grow
Content: Bar chart med 3 bars — "Pre-Claude: 12 leads/mån", "Med Claude: 47 leads/mån", "Med Claude + Growth OS: 89 leads/mån"
Animation:
  - Sequence: bars växer från botten, vänster till höger
  - Timing: 0.6s per bar, 0.2s paus mellan
  - Easing: ease-out
Style:
  - Typography: brand-body-font för labels, brand-mono-font för siffror
  - Colors: muted för bar 1, accent för bar 2, primary för bar 3
  - Stroke/fill: solid fill med 80% transparens
Output format: SVG inline med CSS animation
```

### Transition-wipe (mellan scener)

```
Type: transition-wipe
Content: Helsidan-wipe från vänster till höger
Animation:
  - Sequence: solid panel sveper över skärmen
  - Timing: 0.5s
  - Easing: ease-in-out
Style:
  - Colors: brand primary color
  - Stroke/fill: solid fill, no stroke
Output format: SVG inline
```

## Brand-arv

Hyperframe är extra känsligt för brand-konsekvens eftersom output är kod som direkt använder brand-värden.

Ärv från brand-guidelines.skill.md:

- **Typografi** — font-family, weight, letter-spacing (motion graphics fungerar inte med fel font)
- **Färger** — exakta hex-koder, inte ungefärliga
- **Rytm/easing** — om brand är "premium långsam" → ease-out + längre durations. Om "energisk snabb" → spring + korta durations.

Saknas brand-guidelines: använd Inter + system-defaults och flagga att brand-fil behövs.

## Failure conditions

Säg ifrån om:

- Användaren vill ha komplex 3D eller fotorealistisk content → fel verktyg, använd Higgsfield
- Animation har >10 element som rör sig samtidigt → blir rörig, föreslå färre
- Brand-guidelines saknas och konsekvent stil krävs över flera klipp → flagga problemet

## References

- `higgsfield-prompts` — för 3D-animationer och rendered video där Hyperframe är fel verktyg
- `growth-os-philosophy` — brand-guidelines.skill.md är källan till färger, typografi, rytm
