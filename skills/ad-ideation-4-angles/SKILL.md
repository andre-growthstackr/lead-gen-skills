---
name: ad-ideation-4-angles
description: Generera 12 ad-koncept (4 vinklar × 3 hooks) för B2B-annonsering kopplat till kundresan. Läser icp-summary.md och voc-rapport.md från Project som indata och producerar smärta-fokus, lösnings-fokus, social proof, samt ROI/data-vinklar — varje med 3 hook-varianter. Använd skillen varje gång användaren skriver "ad ideation", "4 vinklar", "12 ad-varianter", "annonsdesign", eller ber om kreativa koncept för Meta/LinkedIn-kampanj. Output distribueras till short-form-video-Skill (L4.2) och image-post-Skill (L4.3) för faktisk produktion.
---

# ad-ideation-4-angles

Skill för att generera annonskoncept-portföljen som matar Modul 6:s kampanjer. Bygger på Modul 4:s pipeline (Content OS, Short Form Video, Image Post) men med kampanjmål istället för organiskt syfte.

## Vad skillen gör

- Läser `icp-summary.md` (idealkund + smärtor) och `voc-rapport.md` (kundernas egna formuleringar) från Project
- Genererar fyra distinkta vinklar kopplade till kundresan: smärta, lösning, social proof, ROI
- Per vinkel: 3 hook-varianter → 12 ad-koncept totalt
- Levererar tabell med koncept, hook, body, CTA och suggested format (video/image)
- Pushar färdiga koncept till Airtable Content OS för produktion via Modul 4-Skills

## Vad skillen INTE gör

- Producerar inte själva videon eller bilden — det gör `create-short-form-video` (L4.2) och `image-post-generation` (L4.3)
- Publicerar inte kampanjen — det gör `meta-ads-cli` (L6.2) och `linkedin-ads` (L6.3)
- Skriver inte ny ICP eller VoC från noll — antar att de finns i Project

## Triggers

Aktivera när användaren skriver:

- "ad ideation"
- "4 vinklar" / "fyra vinklar"
- "12 ad-varianter" / "12 ad-koncept"
- "annonsdesign"
- "creative ideation"
- "ge mig annonskoncept för [produkt/tjänst]"
- "kör vinkel-ideationen"

## Workflow

1. **Hämta indata från Project.** Läs `icp-summary.md` och `voc-rapport.md`. Om någon saknas — fråga användaren innan du börjar. Skriv aldrig vinklar utan VoC i botten.

2. **Identifiera fyra vinklar mappade mot kundresan.** Läs `references/angle-prompts.md` för varje vinkel-prompt och anti-patterns innan första körningen.

   | Vinkel | Kundresan-stadie | Mål |
   |---|---|---|
   | Smärta-fokus | Cold (smärta-medvetenhet) | Triggra erkännande av problem |
   | Lösnings-fokus | Warm (lösnings-jämförelse) | Visa varför din lösning är annorlunda |
   | Social proof | Hot (case studies) | Bevisa att andra som dem fått resultat |
   | ROI/data | Ready (mätbar avkastning) | Konkret siffra på investeringen |

3. **Generera 3 hook-varianter per vinkel.** Varje hook ska vara 6-12 ord, talad svenska, ingen click-bait. Variationen ska testa olika emotionella triggers (frustration, nyfikenhet, FOMO, validering).

4. **Skriv body + CTA per hook.** Body är 2-3 meningar. CTA är specifik handling ("Boka demo", "Ladda ner playbook", "Räkna ut din CAC"), inte "Lär dig mer".

5. **Föreslå format per koncept.** Smärta + Social proof = video (känsla driver). Lösning + ROI = image (siffror och jämförelse). Markera i tabellen.

6. **Leverera output-tabellen.** Se Output-format nedan. Pusha till Airtable Content OS-tabell (`Ad Concepts`-vyn) om användaren godkänner.

## Output-format

Markdown-tabell med exakt dessa kolumner:

```
| # | Vinkel | Hook | Body | CTA | Format | Distribution |
|---|---|---|---|---|---|---|
| 1 | Smärta | [6-12 ord] | [2-3 meningar] | [specifik handling] | Video | L4.2 |
| 2 | Smärta | ... | ... | ... | Image | L4.3 |
| ... | ... | ... | ... | ... | ... | ... |
| 12 | ROI | ... | ... | ... | Image | L4.3 |
```

Totalt 12 rader. Varje vinkel har exakt 3 rader.

Bifoga också:

- **Sammanfattning per vinkel** — 1-2 meningar om VAD vinkeln säger, baserat på VoC-citat
- **Distribution-lista** — vilka koncept som ska till L4.2 (video) respektive L4.3 (image)
- **Reasoning-not** — varför just dessa fyra hooks valdes (max 5 rader)

## Failure conditions

Avbryt och fråga användaren om:

- `icp-summary.md` eller `voc-rapport.md` saknas i Project
- VoC-rapporten har färre än 5 kundcitat — för tunn data för att hitta autentiska vinklar
- Användaren ber om "fler än 12 koncept" — det är inte skillens jobb, hänvisa till volume-varianter via L4.2/L4.3
- Användaren ber om koncept utan att ange produkt/tjänst — fråga efter scope först
- Två hooks blir för lika — bryt och variera mer

## References

- `references/angle-prompts.md` — Per vinkel: vad är "fel sätt", vad är "rätt sätt", VoC-citat-exempel, hook-format-mallar
