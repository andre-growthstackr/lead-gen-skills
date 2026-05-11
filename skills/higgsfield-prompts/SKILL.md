---
name: higgsfield-prompts
description: Cheatsheet för Higgsfield MCP — prompt-disciplin för 3D-animationer, Kling-videoklipp och AI-bilder med kamera-rörelse, miljö, ljus och längd specificerat. Aktivera när användaren skriver "Higgsfield-prompt", "skapa 3D-animation", "Kling video", "Higgsfield bild", "ad-creative i Higgsfield", eller behöver en prompt för en hero-animation eller image-post. Levererar en strukturerad prompt som ärver brand-guidelines.skill.md (färger, stil).
---

# higgsfield-prompts

Quick-reference för prompts till Higgsfield MCP.

## Vad skillen gör

Skriver Higgsfield-prompts med rätt struktur: subject, kamera-rörelse, miljö, ljus, stil, längd. Ärver brand-färger och visuell stil från brand-guidelines.skill.md när det finns.

## Triggers

Aktivera när användaren skriver:

- "Higgsfield-prompt"
- "Skapa 3D-animation"
- "Kling video"
- "Higgsfield bild"
- "Ad-creative i Higgsfield"
- "Hero-animation för landing page"
- "Image-post-bild via Higgsfield"

## Prompt-disciplin

En bra Higgsfield-prompt har sex delar i ordning:

1. **Subject** — vad som visas (objekt, person, scen)
2. **Action** — vad subjektet gör
3. **Camera** — rörelse och vinkel (dolly, pan, orbit, push-in, static)
4. **Environment** — miljö, bakgrund, kontext
5. **Lighting** — ljussättning (cinematic, soft, harsh, neon, golden hour)
6. **Style + duration** — visuell stil och längd i sekunder

## Prompt-template

```
Subject: [vad som visas — konkret, fysiskt]
Action: [vad subjektet gör — verb-fokus]
Camera: [rörelse och vinkel]
Environment: [miljö och bakgrund]
Lighting: [ljus och stämning]
Style: [visuell stil — ärver brand-guidelines om finns]
Duration: [sekunder, för video]
```

## Exempel per use-case

### Hero-animation (landing page background)

```
Subject: Abstract geometric shapes floating in 3D space — cubes, spheres, and torus forms
Action: Slowly rotating and morphing into each other in fluid motion
Camera: Slow orbit at 30-degree angle, smooth
Environment: Deep space gradient, subtle particle field
Lighting: Cool blue rim light from left, soft accent from right
Style: Minimalist 3D render, brand palette [from brand-guidelines.skill.md]
Duration: 8 seconds, loopable
```

### Ad-creative (LinkedIn-feed video)

```
Subject: Hand holding a smartphone showing a dashboard interface
Action: Tilting the phone slightly, screen scrolling smoothly
Camera: Close-up, slight push-in
Environment: Clean white desk, soft shadows, no clutter
Lighting: Soft daylight from above-left, no harsh shadows
Style: Photoreal, product-shot quality
Duration: 6 seconds
```

### Image-post-bild (LinkedIn carousel cover)

```
Subject: Single bold word floating in 3D space — typography-driven
Action: Static (image, no movement)
Camera: Straight-on, slight perspective skew
Environment: Brand-color gradient background [from brand-guidelines]
Lighting: Strong key light from upper left, deep shadow on word
Style: Editorial, magazine-cover feel
Duration: N/A (still)
```

### Produkt-demo (3D-animation)

```
Subject: Product UI rendered as floating 3D card with multiple layers
Action: Layers expanding outward to reveal components, then collapsing back
Camera: Push-in from front, then orbit 45 degrees
Environment: Dark studio background, brand-accent floor reflection
Lighting: Three-point lighting, key from upper-right
Style: Premium SaaS product film, clean and confident
Duration: 12 seconds
```

## Brand-arv från brand-guidelines.skill.md

Om brand-guidelines.skill.md finns, ärv:

- **Färgpalett** — primary, accent, background hex-koder. Använd i Environment och Lighting.
- **Typografi** — om text visas, specificera font-style (serif italic, monospace, etc) som matchar brand
- **Visuell ton** — editorial, technical, playful, premium — påverkar Style-fältet
- **Rörelse-rytm** — om brand har en definierad känsla (snabb-energisk vs långsam-premium)

Refererar inte brand-guidelines om filen saknas — använd då generiska "clean modern" defaults och flagga att brand-fil behövs för konsekvent output.

## Failure conditions

Säg ifrån om:

- Användaren vill generera content med människors ansikten utan rättigheter — bryter mot harmful content-policy om det är specifika personer
- Promptens subject är abstrakt utan visuell anchor ("känslan av framgång") — Higgsfield behöver konkreta objekt eller scener
- Duration > 15 sek — Higgsfield är optimerad för korta klipp, longform behöver annan workflow
- Brand-guidelines saknas och konsekvent visuell stil krävs — flagga att utan brand-fil blir output spretigt mellan klipp

## References

- `hyperframe-prompts` — för motion graphics som kod istället för rendered video
- `growth-os-philosophy` — brand-guidelines.skill.md är källan till all visuell konsekvens
