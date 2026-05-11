# Content-typer — anatomi och regler

Detaljerade specs för de fyra content-typerna som content-ideation-skillen producerar. Läs denna fil innan första ideationen i en session.

## 1. Short form video (SFV)

**Längd:** 15-30 sekunder talat. Räkna 2-3 ord/sekund i tempo → 30-90 ord totalt.

**Plattformar:** LinkedIn (native upload), Instagram Reels, YouTube Shorts. Samma video på alla tre — men varianten i copy/caption justeras per plattform.

### Anatomi

| Sektion | Tid | Ord | Jobb |
|---|---|---|---|
| Hook | 0-3 sek | ~10 ord | Krok in tittaren. Smärta, kontrarian-take, eller siffra. |
| Body | 3-25 sek | 50-75 ord | Ge värdet. 1-2 punkter, max. |
| CTA | 25-30 sek | ~10 ord | Vad ska de göra. Konkret. |

### Hook-bibliotek

Bygg hooks från VoC-citat eller topp-smärtor. Format som funkar:

- **Smärta + push-back:** "Din content fungerar inte. Och det är inte algoritmens fel."
- **Siffra + kontrarian:** "95% av AI-piloterna ger noll ROI. Här är varför."
- **If-then:** "Om din ICP inte kan beskrivas på 30 sekunder — du har ingen ICP."
- **Kort regel:** "Tre saker som dödar din pipeline."
- **Direkt fråga:** "Varför ser all din content samma ut som alla andras?"

**Aldrig:** "Hej alla!", "I den här videon ska jag visa...", "Som marknadsförare vet du att...".

### Plattforms-anpassningar

- **LinkedIn:** Caption 80-150 ord med kontext + en fråga. Sista raden uppmanar till kommentar.
- **IG Reels:** Caption max 50 ord, 3-5 hashtags i slutet.
- **YT Shorts:** Title under 60 tecken, beskrivning 1-2 meningar.

## 2. Image post

**Längd:** Hook + body 90-150 ord + CTA.

**Plattformar:** LinkedIn (native post), Instagram feed. Samma kärninnehåll men bild-format anpassas (LinkedIn = horisontellt 1200×627 eller carousel; IG = kvadratiskt 1080×1080 eller carousel).

### Anatomi

| Sektion | Format | Jobb |
|---|---|---|
| Hook | Första raden, fet | Stoppa scroll. Max 12 ord. |
| Body | 4-8 korta stycken | Ge värdet. En insight, ett ramverk, en regel. |
| CTA | Sista raden, separat | Vad ska de göra — kommentera, klicka, dela. |
| Bild | 1080×1080 (IG) / 1200×627 (LI) | Förstärker hooken, ej dekorativ |

### Image-typer som funkar

- **Quote-card** — citatet från hooken, visuellt
- **Framework-diagram** — 2x2-matris, funnel, eller flow-chart
- **Före/efter** — split-screen som visar transformationen
- **Carousel** (5-10 slides) — för djupare framework eller checklista
- **Skärmdump** — UI-element, prompt, Airtable-vy

### Bild-beskrivning som leverans

Skillen producerar inte bilden — den producerar beskrivningen som användaren tar till Figma/Canva/midjourney:

```
Bild-beskrivning: Carousel, 6 slides.
Slide 1: Hook "95% av AI-pilots ger noll ROI" på cremfärgad bakgrund, serif italic.
Slide 2-5: Varför + tre konkreta orsaker, en per slide.
Slide 6: CTA "Vill du höra hur du fixar det? Skriv 'AI-pilot' i kommentarerna."
```

## 3. Long form video (LFV)

**Längd:** 5-10 min. ~150 ord/min talat → 750-1 500 ord manus.

**Plattformar:** YouTube primärt. Repurpose till LinkedIn-artikel (samma innehåll, omformaterat som läst text + screenshots).

### Anatomi

| Sektion | Tid | Jobb |
|---|---|---|
| Hook | 0-30 sek | Smärta + push-back + cliff |
| Promise | 30-60 sek | Vad du får ut av videon |
| Body | 1-8 min | 3-5 sektioner, var och en ett tydligt moment |
| Demo (om relevant) | 2-4 min | Skärmdelning, exempel, before/after |
| Summering | sista 30 sek | Deliverable + CTA |

### Repurpose till LinkedIn-artikel

LFV-script blir LinkedIn-artikel genom:

1. Manus omformat till skriftspråk (talspråk dras tillbaka)
2. Screenshots från demon klistras in
3. Indelning i H2/H3-sektioner
4. CTA i slutet — kommentera, ladda ner mall, hör av sig

### LFV som ankaret för veckan

LFV är veckans tema-ankare. När LFV är klar — derivera 2-3 SFV från olika klipp i den. Detta är 80% av leverage du får från LFV.

## 4. Newsletter

**Längd:** ~600-1 000 ord totalt. Läses på 5 minuter.

**Plattform:** ESP (HighLevel / MailChimp / HubSpot / Beehiiv). Skickas på fast veckodag.

### Anatomi

| Sektion | Längd | Jobb |
|---|---|---|
| Subject line | <50 tecken | Öppningskrok. A/B-testa. |
| Preheader | <100 tecken | Förstärker subject. |
| Intro | 80-120 ord | Veckans hook + en personlig vinkel |
| Nyhet 1-5 | 60-100 ord per nyhet | Headline + 2-3 meningar + Andrés take + länk |
| Outro / CTA | 50-80 ord | Vad gör läsaren härnäst |
| Footer | Standard | Avregistrera, kontakta |

### Hooks som funkar i subject line

- **Siffra:** "73% av B2B-content är skitskräp"
- **Fråga:** "Varför fungerar inte din content?"
- **Kontrarian:** "Sluta posta varje dag"
- **Curiosity gap:** "Det som hände när vi pausade allt content i 30 dagar"

### Nyhets-urval

Newsletter-typen pluggas in med `industry-news-monitoring`-skillen som hämtar topp-5 nyheter. Content-ideation-skillen producerar bara skelett + Andrés take per nyhet.

## Voice-regler — gäller alla typer

**Skriv så här:**
- Korta meningar. Ofta. Punkt.
- "Du", "vi", direkt tilltal
- Siffra, namn, exakt verktyg
- Push-back där det är förtjänat
- Naturlig svenska — "men", "fast", "ändå"

**Skriv aldrig så här:**
- "I den här posten ska vi utforska..."
- "Som marknadsförare vet du att..."
- "Revolutionerande", "spelförändrande", "transformera", "10x", "unlock"
- "Synergier", "leverera värde"
- "Strategy Layer" → säg "strategi-grunden"
- "Mental karta" → säg "ramverk"
- "Mindset-skifte" → säg "tänket ändras"

## Distribution per content-typ

| Typ | LinkedIn | IG Reels | IG Feed | YT Shorts | YT Long | Newsletter | LI-artikel |
|---|---|---|---|---|---|---|---|
| SFV | x | x | | x | | | |
| Image | x | | x | | | | |
| LFV | | | | | x | | x |
| Newsletter | | | | | | x | |

## Frekvens-regel

3 SFV + 2 image + 1 LFV + 1 newsletter per vecka är taket första kvartalet. Höj inte volymen — höj kvaliteten. Generic content är värre än ingen content.
