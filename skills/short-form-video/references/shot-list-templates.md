# Shot-list-mallar per video-typ

Shot-listen är råinspelnings-planen. Varje rad är en shot — vad som filmas, hur, varför. Mallarna nedan täcker de fyra vanligaste SFV-strukturerna.

## Mall 1: Talking-head-only (15-30 sek)

Klassiskt format. André pratar in i kameran. Captions overlay för betoning, ingen B-roll. Funkar för push-back / direkta åsikter / kontrarian-takes.

| # | Tid | Shot-typ | Innehåll | Overlay |
|---|---|---|---|---|
| 1 | 0-3 sek | Midshot framifrån | Hook talad direkt i kameran | Hook-caption (Hyperframe #1) |
| 2 | 3-25 sek | Samma framing | Body — 1-2 punkter | Lower-third per nyckelfras (Hyperframe #6) |
| 3 | 25-30 sek | Samma framing | CTA | CTA-card (Hyperframe #5) i sista 2 sek |

**Inspelnings-noteringar:**
- En tagning, hela manus
- Bra ljus (nyckelljus från vänster, fill rätt sida)
- Bakgrund blurrad eller renoverad
- Blickkontakt hela tiden — ingen ner-på-anteckningar-titt

## Mall 2: Talking-head + Screen-share (15-30 sek)

Talking-head för hook + CTA. Screen-share i mitten för att visa exempel/demo. Funkar för "så här gör du"-typer och produkt-demos.

| # | Tid | Shot-typ | Innehåll | Overlay |
|---|---|---|---|---|
| 1 | 0-3 sek | Midshot framifrån | Hook | Hook-caption |
| 2 | 3-8 sek | Talking-head | Setup för demo: "Här är vad du gör" | – |
| 3 | 8-22 sek | Screen-share | Demo med röst-over | Cursor-highlight (animerad ring) |
| 4 | 22-30 sek | Tillbaka till talking-head | CTA | CTA-card |

**Inspelnings-noteringar:**
- Spela in talking-head + screen-recording separat
- Klipp ihop i post — talking-head fadar/cuttar till screen-share
- Skärmen ska vara läsbar i 9:16 — zooma in på viktiga UI-element

## Mall 3: Screen-only-walkthrough (20-40 sek)

Bara skärmdelning med röst-over. Funkar för verktyg-genomgångar, prompt-demos, Airtable-walkthroughs.

| # | Tid | Shot-typ | Innehåll | Overlay |
|---|---|---|---|---|
| 1 | 0-3 sek | Statisk skärm | Hook talad som röst-over | Hook-caption över skärmen |
| 2 | 3-25 sek | Skärm-rörelse | Demo, klick-för-klick | Cursor-highlight + lower-third-captions |
| 3 | 25-30 sek | Slut-skärm med resultat | CTA röst-over | CTA-card |

**Inspelnings-noteringar:**
- Skärm-recorder i 1920×1080 sedan croppa till 9:16
- Eller direkt 1080×1920 om verktyget stödjer det
- Cursor ska vara synlig — använd zoom om text är liten
- Röst-over spelas in separat, synca i post

## Mall 4: Before/After (15-25 sek)

Visa transformation. Funkar för case studies, framework-demos, "innan jag använde X / efter jag använde X".

| # | Tid | Shot-typ | Innehåll | Overlay |
|---|---|---|---|---|
| 1 | 0-3 sek | Talking-head | Hook | Hook-caption |
| 2 | 3-12 sek | Split-screen Before/After | Visualisering av före | Before/After Hyperframe (#4) |
| 3 | 12-22 sek | Talking-head | "Här är vad som hände" | Lower-third per punkt |
| 4 | 22-25 sek | CTA-card | CTA | CTA-card Hyperframe |

**Inspelnings-noteringar:**
- Before/After-bilden kan vara screenshots, eller mocked screens
- Hyperframe-animation gör split-screen-revealen
- Talking-head ska sluta innan CTA-card visas — ingen voice-over CTA-cardet

## Shot-spec — standardformat

För varje shot i listen, fyll i:

```
Shot {N} ({TID}):
- Typ: talking-head / screen-share / B-roll / overlay-only
- Framing: midshot / closeup / wide
- Bakgrund: [beskrivning]
- Ljus: nyckelljus + fill / naturligt / skärm-ljus
- Tonalitet: direkt / energetic / lugn / push-back
- Talad text: "[exakt manus-rad]"
- Caption-overlay: [Hyperframe-ID + text]
- B-roll om relevant: [vad filmas]
```

## Higgsfield-AI-genererad B-roll

Om en shot är B-roll och användaren inte vill filma själv — använd Higgsfield. Prompt-format:

```
Higgsfield prompt #{N}:
{BESKRIVNING AV SCEN}
Style: {BRAND-VISUAL-STYLE}
Mood: {TONALITET}
Camera: {STATIC / SLOW-PAN / TRACKING}
Duration: {SEKUNDER}
Resolution: 1080×1920 (9:16)
```

Exempel:

```
Higgsfield prompt #2:
A clean minimalist office desk with a laptop showing an Airtable dashboard.
Style: modern Scandinavian office, soft natural light from left window.
Mood: focused, professional.
Camera: slow pan right over the desk.
Duration: 5 seconds.
Resolution: 1080×1920 (9:16)
```

## Cap-rules

- **Hooken är alltid talking-head eller siffra-overlay.** Inte B-roll. Du behöver Andrés ansikte i de första 3 sekunderna för att skapa förtroende.
- **CTA är alltid CTA-card eller talking-head.** Inte B-roll.
- **B-roll fyller mellanrummet, inte hela videon.** Maximera Andrés närvaro.
- **Captions ska vara läsbara utan ljud.** 85% av SFV ses utan ljud — captions är obligatoriska.
