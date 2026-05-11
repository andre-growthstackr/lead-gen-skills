---
name: short-form-video
description: Produktions-pipeline för short form video från idé till färdig fil — script, shot-list, Hyperframe motion graphics som kod, B-roll-suggestions, captions och slut-rendering. Bygger på Hyperframe + Higgsfield-flödet från Modul 4 lektion L4.2. Ärver brand-guidelines.skill.md för motion-färger och animation-rytm. Pushar slut-leverans tillbaka in i Airtable Content OS. Använd den varje gång användaren skriver "skapa short form video", "video pipeline", "scripta SFV", eller hänvisar till Modul 4 L4.2. Använd även när användaren bara har en idé som ska bli video — denna skill ÄR pipeline-en från Content OS-idé till uppladdningsbar fil.
---

# short-form-video

Skill för Modul 4 lektion L4.2 — Hyperframe + Higgsfield-pipeline för short form video. Tar en idé från Content OS och kör den genom 7 steg till färdig fil.

## Vad denna skill ÄR till för

- Generera komplett script (Hook/Body/CTA) för en SFV
- Generera shot-list för rå-inspelning
- Generera Hyperframe-prompts för motion graphics som kod (SVG/CSS/Lottie)
- Generera B-roll-suggestions användaren kan filma själv eller hitta i stockbibliotek
- Baka in B-roll, captions och motion graphics i slutfilen
- Pusha slutleverans tillbaka till Airtable Content OS

## Vad denna skill INTE gör

- Generera idéer från ICP/VoC → använd `content-ideation`
- Spela in eller filma rå-materialet — det gör André själv
- Lägga upp filen på LinkedIn/IG/YT — det gör André eller HighLevel
- Bygga design-system för motion graphics → använd `brand-guidelines.skill.md`

## Triggers

Skillen aktiveras när användaren skriver:

- "skapa short form video"
- "video pipeline"
- "scripta SFV [idé-ID]"
- "kör short form video för [idé]"
- "produktion video [titel]"
- "Hyperframe-prompt för [video]"
- Hänvisning till Modul 4 / L4.2

## Inputs som behövs innan du börjar

1. **Idé från Content OS** — Idé-ID eller minst Hook + Vinkel. Plocka från Airtable via MCP.
2. **Brand guidelines** — från `brand-guidelines.skill.md` i Project. För motion-färger, font, animation-rytm.
3. **ICP-spec + VoC-rapport** — för att kunna ge förslag på B-roll-vinklar som resonerar.
4. **Aspect ratio** — default 9:16 (vertikalt). Avvik bara på begäran.
5. **Max längd** — default 30 sek. Kan vara 15-60 sek.

Om idén saknas eller är vag — fråga André innan du börjar. Generic video är värre än ingen video.

## Den 7-stegs-pipelinen

### Steg 1: Idé från Content OS

Hämta idéen från Airtable Idéer- eller Content-tabell. Verifiera att:

- Hook är klar (max 15 ord)
- Vinkel är klar (1 mening, kontroversiellt/oväntat/specifikt)
- Body-outline finns (3-5 punkter)
- CTA finns (1 mening)
- ICP-smärta + VoC-citat är mappade

Om något fattas — gå tillbaka till `content-ideation` och fixa idéen först.

### Steg 2: Scripta videon

Producera fullt script enligt 3-akts-struktur:

| Akt | Tid | Ord | Innehåll |
|---|---|---|---|
| Hook | 0-3 sek | ~10 | Den exakta hooken från idéen, talad |
| Body | 3-25 sek | 50-75 | 1-2 punkter, konkret |
| CTA | 25-30 sek | ~10 | Vad ska tittaren göra |

Output: script i format som mappar mot shot-listen. Varje rad i scriptet får en shot-ID.

### Steg 3: Generera shot-list

För varje rad i scriptet — generera en shot-spec:

```
Shot 1 (Hook, 0-3 sek):
- Talking-head, framifrån, midshot
- Bakgrund: kontorsmiljö, blurrad
- Ljus: nyckelljus från vänster
- Tonalitet: direkt blickkontakt, allvarlig (matchar push-back-hook)
- Caption-overlay: "Hook-texten" (motion in från botten)

Shot 2 (Body punkt 1, 3-13 sek):
- B-roll: skärmdump av Airtable Content OS
- Voice-over från André
- Caption-overlay: nyckelfras

[fortsätt för alla shots]
```

Shot-list-mallar per video-typ — i `references/shot-list-templates.md`.

### Steg 4: Hyperframe-prompts (motion graphics som kod)

Hyperframe är Claude-baserade motion graphics — SVG/CSS/Lottie genererat som kod. Skillen genererar en Hyperframe-prompt per motion-graphics-element:

```
Hyperframe-prompt #1 (Hook-caption):
Generate an SVG + CSS animation:
- Text: "95% av AI-pilots ger noll ROI"
- Font: [från brand guidelines]
- Color: [från brand guidelines]
- Animation: text appears letter-by-letter over 0.8s, then settles
- Duration: 3s total (text-in + hold)
- Aspect ratio: 9:16, text positioned bottom third
- Export: WebM with alpha channel
```

Hyperframe-prompts-bibliotek per content-typ — i `references/hyperframe-prompts.md`.

### Steg 5: B-roll-suggestions

För varje shot som är B-roll (alltså inte talking-head) — föreslå 2-3 alternativ:

- **Option A:** användaren filmar själv (specifikt vad)
- **Option B:** Higgsfield AI-genererad video (med prompt)
- **Option C:** stock-footage (Artgrid/Pexels med söktermer)

Användaren väljer och filmar/laddar upp. Skillen tar emot råfilerna och går till steg 6.

### Steg 6: Baka in B-roll, captions, motion graphics

När råmaterial finns:

1. Sätt ihop talking-head + B-roll enligt shot-list-ordning
2. Lägg på captions enligt scriptet
3. Lägg på Hyperframe-motion-graphics enligt shot-list
4. Render som .mp4, 9:16, 1080×1920, H.264, max 60 MB

Detta steg kräver att skillen pratar med ett externt verktyg (FFmpeg, Tella, Descript, CapCut). Om inget MCP är anslutet — generera ett "produktions-recept" som användaren kör manuellt:

```
Produktions-recept för [titel]:
1. Öppna [verktyg]
2. Importera shots [lista]
3. Klipp enligt shot-list (link)
4. Lägg på Hyperframe-export 1, 2, 3
5. Render H.264 9:16
```

### Steg 7: Push till Content OS

När .mp4 är klar:

1. Ladda upp till Airtable Content-tabell, fält "Filer"
2. Uppdatera Content-tabellens Status till "Klar för publish"
3. Skapa Distribution-records för LinkedIn, IG Reels, YT Shorts
4. Sätt plattform-specifik copy för varje (LinkedIn-caption är längre, IG-caption är kortare, YT Shorts title är specifikt)

## Output-format

### Leverans-paket per video

```
# SFV: [Hook] (Idé-ID [N])

## Script
[full script med shot-ID]

## Shot-list
[full lista, en shot per rad]

## Hyperframe-prompts
[en eller fler prompts, en per motion-graphics-element]

## B-roll-suggestions
[per shot — 2-3 options]

## Produktions-recept
[steg-för-steg för slutmontering]

## Distribution-copy
- LinkedIn caption: [...]
- IG caption: [...]
- YT Shorts title + description: [...]
```

Om Airtable-MCP är ansluten — pusha varje sektion till rätt fält i Content-tabellen.

## Failure conditions

Skillen ska INTE leverera om:

- Idéen är vag (ingen klar hook eller vinkel) — gå tillbaka till `content-ideation`
- Brand guidelines saknas — risk för fel motion-färger, fel font
- Användaren ber om en SFV som är > 60 sek — det är inte SFV, det är något annat
- Användaren ber om scripts utan ICP-mappning — generic video är värre än ingen video

## References

- `references/hyperframe-prompts.md` — exempel-prompts per content-typ (hook-overlay, framework-diagram, before/after, siffra-reveal, CTA-card)
- `references/shot-list-templates.md` — shot-list-mallar per video-typ (talking-head-only, talking-head + screen, screen-only-walkthrough, before/after)

## Self-check (obligatorisk innan leverans)

- [ ] Script är 50-90 ord totalt (15-30 sek talat)
- [ ] Hook är max 15 ord, max 3 sek
- [ ] CTA är konkret och en mening
- [ ] Shot-list mappar 1:1 mot script-rader
- [ ] Minst en Hyperframe-prompt genererad
- [ ] B-roll-suggestions ger användaren 2-3 alternativ per shot
- [ ] Brand-färger och font specificerade i alla Hyperframe-prompts
- [ ] Aspect ratio 9:16 specificerad genomgående
- [ ] Distribution-copy producerad för LinkedIn + IG + YT Shorts (olika per plattform)
- [ ] Inga AI-tells i script (sökord-check)
- [ ] Inga förbjudna ord (kör grep mot brand guidelines)
