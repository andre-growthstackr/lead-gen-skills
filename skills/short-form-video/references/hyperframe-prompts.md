# Hyperframe-prompts per content-typ

Hyperframe är motion graphics genererat som kod (SVG/CSS/Lottie) via Claude. Detta dokument är ett bibliotek med exempel-prompts per content-typ — copy/paste till Claude, justera variablerna i {curly braces}, exportera som WebM med alpha-kanal eller transparent MP4.

## Princip

Hyperframe-prompter ska alltid specificera:

1. **Format** — SVG + CSS-animation, eller Lottie-JSON
2. **Aspect ratio** — 9:16 för SFV (1080×1920) eller 1:1 för IG-feed (1080×1080)
3. **Färg + font** — från `brand-guidelines.skill.md`
4. **Animation-duration** — i sekunder, exakt
5. **Position på canvas** — top third / middle / bottom third
6. **Export-format** — WebM med alpha för komposit, eller transparent PNG-sekvens

## Prompt-bibliotek

### 1. Hook-overlay (text appears letter-by-letter)

```
Generate an SVG + CSS animation:
- Text: "{HOOK-TEXT}"
- Font: {BRAND-FONT}, weight 700, size 64px
- Color: {BRAND-PRIMARY} on transparent background
- Animation: text appears letter-by-letter over 0.8s using staggered opacity transitions, then settles
- Hold: 2.2s total visibility
- Aspect ratio: 9:16 (1080×1920)
- Position: bottom third, horizontally centered, max 80% width
- Export: WebM with alpha channel, 30fps
```

### 2. Siffra-reveal (statistic with count-up animation)

```
Generate an SVG + CSS animation:
- Big number: {NUMBER} (e.g. 95%)
- Subtext: "{SUBTEXT}" (e.g. "av AI-pilots ger noll ROI")
- Font number: {BRAND-DISPLAY-FONT}, weight 900, size 240px
- Font subtext: {BRAND-FONT}, weight 500, size 48px
- Color: {BRAND-PRIMARY} on {BRAND-CANVAS-BG}
- Animation: number counts from 0 to {NUMBER} over 1.2s, then subtext fades in over 0.4s
- Hold: 2.5s total
- Aspect ratio: 9:16
- Position: center, stacked vertically
- Export: WebM with alpha, 30fps
```

### 3. Framework-diagram (2x2 matrix reveal)

```
Generate an SVG + CSS animation showing a 2x2 matrix:
- Title: "{FRAMEWORK-TITLE}"
- X-axis label: "{X-AXIS}"
- Y-axis label: "{Y-AXIS}"
- 4 quadrant labels: "{Q1}", "{Q2}", "{Q3}", "{Q4}"
- Colors: axes and lines in {BRAND-PRIMARY}, quadrant fills in {BRAND-ACCENT} with 0.1 opacity
- Animation: axes draw in over 0.6s, then quadrant labels fade in sequentially (0.3s each)
- Hold: 3s total
- Aspect ratio: 9:16
- Position: matrix centered, leaves space for caption at bottom
- Export: WebM with alpha, 30fps
```

### 4. Before/After comparison

```
Generate an SVG + CSS animation, split-screen:
- Left half: "Före" with content {BEFORE-DESCRIPTION}
- Right half: "Efter" with content {AFTER-DESCRIPTION}
- Divider: vertical line {BRAND-PRIMARY}, animated to slide in from top over 0.5s
- Labels: {BRAND-FONT}, weight 700, 56px
- Animation: divider slides in, then before-content fades in left (0.4s), then after-content fades in right (0.4s)
- Hold: 3s total
- Aspect ratio: 9:16
- Export: WebM with alpha, 30fps
```

### 5. CTA-card (end frame with action)

```
Generate an SVG + CSS animation for end-card:
- Main text: "{CTA-TEXT}" (max 12 words)
- Secondary text: "{SECONDARY}" (e.g. handle eller URL)
- Font main: {BRAND-DISPLAY-FONT}, italic, 80px
- Font secondary: {BRAND-FONT}, 36px
- Color: {BRAND-ACCENT} on {BRAND-CANVAS-BG}
- Animation: main text slides in from right over 0.6s, secondary fades in 0.3s after
- Hold: 3s
- Aspect ratio: 9:16
- Position: vertically stacked, center
- Export: WebM with alpha, 30fps
```

### 6. Lower-third caption (subtitle bar)

```
Generate an SVG + CSS animation for a lower-third caption:
- Text: "{CAPTION-TEXT}"
- Style: pill-shaped background, {BRAND-PRIMARY} fill, white text
- Font: {BRAND-FONT}, weight 600, 38px
- Animation: slides up from below over 0.3s, holds, slides down after {HOLD-DURATION}s
- Aspect ratio: 9:16
- Position: bottom 15% of frame, horizontally centered, padding 24px
- Export: WebM with alpha, 30fps
```

### 7. List/Checklist reveal

```
Generate an SVG + CSS animation showing a list:
- Items: ["{ITEM-1}", "{ITEM-2}", "{ITEM-3}"]
- Checkmark icon: simple SVG checkmark in {BRAND-ACCENT}
- Font: {BRAND-FONT}, weight 500, 44px, color {BRAND-PRIMARY}
- Animation: items fade in sequentially (0.5s each), checkmark appears after item text (0.2s delay)
- Hold: 1s after last item appears
- Aspect ratio: 9:16
- Position: center, left-aligned with 15% left margin
- Export: WebM with alpha, 30fps
```

## Tips för Hyperframe-prompts

- **Ge alltid exakta värden.** "Stor text" är värdelöst — säg "size 64px".
- **Specificera färg som hex eller named CSS-variabel.** Inte "blå".
- **Definiera timing exakt.** "Animeras in" är värdelöst — säg "0.4s ease-out".
- **Export med alpha-kanal är default.** Annars kan du inte komposita över video.
- **9:16 alltid för SFV.** Om du gör IG-feed-image är det 1:1.
- **Testa i Claude först.** Generera SVG/CSS, öppna i browser, justera. Sedan export till video.

## Komposit-flöde

Hyperframe-output (WebM med alpha) kompositas över talking-head- eller B-roll-video i:

- **FFmpeg** — `ffmpeg -i base.mp4 -i overlay.webm -filter_complex "overlay=0:0" out.mp4`
- **CapCut** — drag-and-drop overlay-track ovanför video-track
- **Descript** — composition view, layer overlay above video
- **Premiere/Final Cut** — drop som overlay-clip på spår över video-spåret
