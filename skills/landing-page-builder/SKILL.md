---
name: landing-page-builder
description: Bygg en konverterande landningssida för en lead magnet, ett webinar/event eller en sales-page. Struktur: Hero (italicerat utfallsord i coral) + Pain-section + Solution + Social proof + CTA + FAQ. Output är en single-file HTML/CSS-sida deploy-redo till Vercel, brand-applicerad via `brand-guidelines.skill.md` och pain-driven via `voc-rapport.md`. Aktivera när användaren skriver "bygg landningssida", "landing page för X", "kampanj-sida", "bygg sales-page", "bygg webinar-sida", eller refererar till L3.3 praktiska delen ("länka landningssidan till lead magnet:en"). Optional: Higgsfield-integration för 3D-hero-animation (samma teknik som L3.1). Skill:en triggar även när användaren bara säger "gör en sida för [lead magnet]".
---

# landing-page-builder

Skill för att bygga landningssidor som konverterar. Inte tema-baserade WordPress-sidor. Inte byggsatser. Single-file HTML/CSS med brand-konsistent struktur, pain-driven copy och en tydlig CTA.

## Vad skillen gör

- Bygger landningssidor i tre kategorier:
  - **Lead magnet-sida** — driver email-capture till en lead magnet (från `lead-magnet-builder-interactive`)
  - **Webinar/event-sida** — driver registrering till ett event
  - **Sales-page** — driver köp eller demo-bokning
- Använder en standard-struktur: Hero / Pain / Solution / Social proof / CTA / FAQ
- Pain-section drivs av `voc-rapport.md` (citerar leadets eget språk)
- Brand-applicerad via `brand-guidelines.skill.md`
- Output: single-file HTML/CSS + Vercel-deploy-instruktion
- Optional: Higgsfield 3D-animation i hero (samma teknik som L3.1 — mp4 + scroll-mapping)

## Vad skillen INTE gör

- Bygger inte själva lead magneten → `lead-magnet-builder-interactive`
- Bygger inte hela hemsidan med flera sidor → använd L3.1-mallen + Claude Code direkt
- Sätter inte upp tracking / analytics → flagga som nästa steg, men inte i scope
- Skriver inte annons-copy för att driva trafik till sidan → `marketing-content-generator` (Modul 4)

## Triggers

Aktivera vid:

- "bygg landningssida"
- "landing page för [lead magnet / webinar / produkt]"
- "kampanj-sida"
- "bygg sales-page"
- "bygg webinar-sida"
- "länka landningssidan till lead magnet:en" (L3.3 praktisk)
- "gör en sida för [lead magnet-namn]"
- "bygg en sida som konverterar för X"

## Input som krävs

1. **Sid-typ** — lead magnet / webinar / sales-page
2. **Vad sidan säljer / capture:ar** — t.ex. "T1 pain awareness-quiz", "Marketing automation webinar 12 nov", "Demo av X"
3. **`voc-rapport.md`** — för pain-section
4. **`brand-guidelines.skill.md`** — för färg/typografi/voice
5. **Hero-headline-tema** — vad utfallsordet ska vara (det italicerade ord i coral)
6. **CTA-action** — vart knappen leder (lead magnet-URL, kalenderbokning, checkout)

Om något saknas: fråga.

## Struktur — alla landningssidor

Sex sektioner. Inga andra. Skala längd, inte antal sektioner.

### 1. Hero

- **H1** med ett italicerat utfallsord i coral (GrowthStackr-signatur)
- Format: "Du behöver inte mer *content* — du behöver ett *system*"
- En sub-headline (1 mening, 15-25 ord) som specificerar för vem
- Primary CTA-knapp (samma som footer-CTA)
- Optional: hero-bild eller 3D-mp4 (om Higgsfield-integration används)

### 2. Pain-section

- Rubrik: "Det här känner du igen" eller "Du har förmodligen redan testat..."
- 3-4 bullets med pains från `voc-rapport.md` — citera eller paraphrase användarens eget språk
- Sista bullet är en push-back på vad de redan testat ("Och AI-piloter? De har gett dig noll ROI hittills, eller hur?")

### 3. Solution

- Rubrik: vad du faktiskt erbjuder (lead magnet / webinar / produkt)
- 3-5 bullets med outcomes — inte features. Outcome = "Du har en pipeline-rapport på måndagar", inte "Vi har CRM-integration".
- Mini-visualisering om relevant (screenshot, ikoner, diagram)

### 4. Social proof

- Antingen: 2-3 case study-citat med namn + titel + företag
- Eller: logos från företag som använt produkten
- Eller: en specifik metric ("345 leads booked på 3 månader")
- Om inga case studies finns: skippa sektionen, lägg in en placeholder och flagga till användaren

### 5. CTA

- Samma CTA-knapp som hero
- En sub-mening som tar bort friktion ("Tar 2 min", "Ingen kreditkort", "Email räcker")
- Direkt eller indirekt urgency ("Vi tar in 10 founders i wave 1")

### 6. FAQ

- 4-6 frågor som adresserar invändningar du sett i VoC
- Format: "Är det här för mig om jag är solo?" → svar i 1-2 meningar
- Sista frågan: "Vad händer om jag inte är nöjd?" eller motsvarande garanti-fråga

## Workflow

### Steg 1: Klargör sidtyp + asset

Vilken av tre sid-typer? Vad är CTA-action? Vilken lead magnet/webinar/produkt?

### Steg 2: Skanna VoC för pain-bullets

Läs `voc-rapport.md`. Plocka 3-4 pains som bäst matchar målgruppen för sidan. Citera språket — inte paraphrasera till neutralt.

### Steg 3: Designa hero-headline

H1:n är hela poängen. Format:

> "Du behöver inte mer *content* — du behöver ett *system*"
>
> "Stoppa *churn:en* — bygg en *pipeline* som värmer själv"
>
> "Få *leads* från LinkedIn — utan *cold outreach*"

Utfallsordet i coral-italic är GrowthStackr-signatur. Default-färg: `#FF6B6B`. Italic via `<em>` med inline CSS.

### Steg 4: Skriv copy block-för-block

I ordning: Hero → Pain → Solution → Social proof → CTA → FAQ. Skriv inte sista först.

Voice-checkpoint per block: läs högt. Snubblar du? För långt eller AI-tonalt. Bryt upp.

### Steg 5: Lägg in CTA-action

Knappen ska leda till:

- **Lead magnet-sida** → URL till lead magneten (från `lead-magnet-builder-interactive`)
- **Webinar-sida** → Calendly/registreringssida eller direkt form
- **Sales-page** → checkout-URL eller demo-bokning

### Steg 6: Brand-applicera

Inherit `brand-guidelines.skill.md`. Färg, typografi, knapp-stil. Defaults om brand saknas: GrowthStackr (coral #FF6B6B, cream bg, near-black text, Inter + Instrument Serif).

### Steg 7: Optional — Higgsfield 3D-hero

Om användaren vill ha 3D-animation i hero (samma teknik som L3.1):

1. Generera 5-sek cinematic mp4 i Higgsfield/Kling med specifik prompt
2. Splitta till ~150 frames med FFmpeg
3. Bygg scroll-mapping i JavaScript så frame följer scroll-position
4. Lägg in i hero-sektionen som `<canvas>`-element

Om användaren inte specificerar: skippa, statisk bild eller ren typografi-hero räcker.

### Steg 8: Deploy

```bash
vercel deploy --prod
```

Eller via Claude Code. Koppla domän om relevant.

## Output-format

1. **HTML-fil:** `landing-[asset-namn].html` (single-file, inline CSS)
2. **Deploy-instruktion:** 3 steg (öppna fil → vercel deploy → koppla domän)
3. **Tracking-checklist:** vad användaren ska sätta upp innan trafik körs (GA4 / GTM / Pixel — out of scope men flagga)

## Failure conditions

Lever inte sidan om:

- Hero saknar italicerat utfallsord (GrowthStackr-signatur missas)
- Pain-section är generic — pains måste komma från VoC
- Solution-bulletsen är features, inte outcomes
- Social proof-sektionen är tom utan flag till användaren
- CTA-knappen leder ingenstans (broken link eller placeholder #)
- Sidan inte fungerar mobile (responsive saknas)
- AI-tells i copyn ("I den här guiden kommer du att lära dig...", "Låt oss dyka rakt in i...")
- Sidan över 200KB single-file (för många bilder eller externa beroenden)

## Anti-patterns

- "Vi är experter på [X]" som hero — kandidat-CV, inte landningssida
- Pain-section som beskriver problemet generisk ("Marknadsföring är svårt") — citera leadets eget språk istället
- "Boka en demo nu!" som CTA utan vad demon faktiskt levererar — friktion utan reward
- FAQ med generic Q&A ("Vad är ert företag?") — adressera invändningar, inte introducera dig
- Stockbild-människor i hero — säg mer om att sidan är AI-genererad än något annat

## References

- `references/struktur-template.md` — full markdown-mall som du fyller i sektion för sektion
- `references/hero-headline-patterns.md` — 20+ exempel på italicerat-utfallsord-format
- `references/voc-pain-mapping.md` — hur du översätter VoC-pains till pain-section-bullets utan att tappa språket
- `references/higgsfield-hero-prompts.md` — färdiga prompts för 5-sek cinematic 3D till hero (inherit från Modul 2 higgsfield-prompts-skill)
- `references/cta-pattern-bibliotek.md` — knapp-copy + sub-friction-meningar för tre sid-typer

## Voice-regler (hård lista)

- Italicerat utfallsord i coral i H1 — obligatoriskt
- Korta meningar i copy. Ofta. Punkt.
- Push-back i pain-section — minst en mening
- Outcome-formulerade bullets i solution-sektion
- Inga AI-tells, inga hype-ord (revolutionerande, spelförändrande, 10x, transformera, unlock, synergier, leverera värde)
- Inga emoji
- Naturlig svenska där sidan är på svenska
