---
name: lead-magnet-builder-interactive
description: Bygg en interaktiv HTML/JS-lead magnet (kalkylator, quiz eller assessment) som samlar email + leadets svar. Tar antingen en idé direkt från användaren eller plockar topp-tre från `lead-magnet-ideer.md`. Output är en deploy-redo HTML-fil med email-capture, integrations-hook (Webhook eller MCP till HubSpot/HighLevel) och Vercel-deploy-instruktion. Aktivera när användaren skriver "bygg interaktiv lead magnet", "bygg kalkylator", "bygg quiz", "bygg assessment", "konvertera lead magnet-idé till HTML", eller bara "bygg lead magnet:en". Skill:en triggar även när användaren refererar till L3.3 praktisk del ("kör build-prompten"). Inherit:ar `brand-guidelines.skill.md` (färger, typografi, voice) — outputten måste vara brand-konsekvent från start. Templates ligger i `references/templates/`.
---

# lead-magnet-builder-interactive

Skill för att bygga interaktiva lead magnets — inte PDF:er, inte whitepapers, inte e-böcker. Tre format: kalkylator, quiz, assessment. Ett email-capture-fält. En integrations-hook till CRM. Deployment till Vercel.

## Vad skillen gör

- Tar input: en lead magnet-idé (från användaren eller från `lead-magnet-ideer.md`)
- Identifierar rätt format av tre: kalkylator / quiz / assessment
- Startar från en HTML-template i `references/templates/` och bygger ut
- Brand-applicerar via `brand-guidelines.skill.md` (färg, typografi, voice)
- Lägger in email-capture-fält + integrations-hook (Webhook till HubSpot/HighLevel eller MCP-call)
- Skriver färdig HTML-fil + deploy-instruktion för Vercel
- Verifierar att email + leadets svar pushas till CRM

## Vad skillen INTE gör

- Bygger inte landningssida som driver trafik till lead magnet:en → använd `landing-page-builder`
- Tar inte fram idén → använd `lead-magnet-ideation`
- Bygger inte sekvensen som triggas efter ifyllning → använd `crm-pipeline-mcp`
- Skriver inte SEO-copy för sidan — det är `landing-page-builder` job

## Triggers

Aktivera vid:

- "bygg interaktiv lead magnet"
- "bygg kalkylator"
- "bygg quiz"
- "bygg assessment"
- "konvertera lead magnet-idé till HTML"
- "kör build-prompten" (referens till L3.3 praktisk del)
- "bygg lead magnet:en från lead-magnet-ideer.md"
- "bygg [namnet på lead magnet:en]"

## Input som krävs

1. **Lead magnet-idé** — antingen:
   - Användaren beskriver idén direkt ("bygg en ROI-kalkylator för marketing automation")
   - Användaren refererar till `lead-magnet-ideer.md` ("bygg T1" eller "bygg topp-1 idé")
2. **`brand-guidelines.skill.md`** från Modul 2 (för färg/typografi/voice) — inherit
3. **CRM-info** — vilken CRM (HubSpot eller HighLevel) leads ska pushas till, plus webhook-URL eller bekräftelse att MCP är ansluten
4. **Domän eller Vercel-projekt** där lead magnet:en ska deployas

Om idé eller brand-guidelines saknas: fråga. Om CRM saknas: bygg lokalt först, lägg in webhook-stub, säg åt användaren att fylla i URL innan deploy.

## Workflow

### Steg 1: Identifiera format

Mappa idén till ett av tre format:

| Format | När det passar | Template |
|---|---|---|
| **Kalkylator** | Användaren ska räkna ut ett tal (ROI, pris, tid, kostnad) | `references/templates/kalkylator.html` |
| **Quiz** | Användaren ska få en kategori-baserad output ("Du är typ X") eller score | `references/templates/quiz.html` |
| **Assessment** | Multistep-formulär som ger en personlig rapport / rekommendation | `references/templates/assessment.html` |

Om idén är otydlig: fråga vilket format som passar bäst.

### Steg 2: Definiera input-fält

**Kalkylator:**
- 3-7 numeriska input-fält (antal kunder, månadskostnad, conversion rate osv.)
- Tydliga labels med hjälp-text
- Validering: bara siffror, range som gör mening

**Quiz:**
- 5-10 frågor med 3-5 svarsalternativ per fråga
- Varje svar har en poäng/kategori-tag
- Slutet: scoring-logik som mappar svar till output-kategori

**Assessment:**
- 4-8 steg (multistep-formulär)
- Mix av val (radio/checkbox) och kortsvar
- Sista steget: kontaktinfo (email obligatoriskt)
- Slutet: skräddarsydd rapport som visar specifika rekommendationer baserat på svaren

### Steg 3: Definiera output / resultat-sida

**Kalkylator:**
- Visar uträknat tal stort och tydligt
- En förklarande mening: "Du sparar X tim/mån = Y kr/år"
- CTA: "Få den fulla rapporten på email" → email-capture

**Quiz:**
- Score eller kategori-output (t.ex. "Du är en early-stage operator")
- 2-3 specifika rekommendationer baserat på kategorin
- CTA: "Få din personliga handlingsplan på email"

**Assessment:**
- Personlig rapport med 3-5 sektioner (din situation, dina svagheter, dina åtgärder, nästa steg)
- Rapporten visas på sidan + skickas till email
- CTA: "Bokar 15 min med oss för att gå igenom rapporten"

### Steg 4: Email-capture + CRM-integration

Email-fältet är obligatoriskt. Två alternativ för var det placeras:

- **Gating** — användaren måste fylla i email innan resultatet visas (högre conversion till lead, lägre completion-rate)
- **Post-result** — användaren ser resultatet direkt, email frågas för "fördjupad rapport" eller "spara resultatet" (lägre lead-rate, högre engagement)

Default: gating för MOFU/BOFU, post-result för TOFU. Användaren kan välja annorlunda.

**Integration-hook:**

```javascript
// Default: webhook POST till HubSpot eller HighLevel
async function submitLead(payload) {
  const WEBHOOK_URL = process.env.CRM_WEBHOOK_URL; // fyll i vid deploy
  await fetch(WEBHOOK_URL, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      email: payload.email,
      lead_magnet: 'T1-pain-awareness-quiz', // mappar mot ideer.md
      score: payload.score, // eller assessment-svar
      submitted_at: new Date().toISOString(),
      source: 'lead-magnet-builder'
    })
  });
}
```

Om CRM körs via MCP (HubSpot/HighLevel MCP-connector): hooka in direkt mot MCP-call istället för webhook.

### Steg 5: Brand-applicera

Inherit från `brand-guidelines.skill.md`:

- Färgpalett (primary, secondary, accent)
- Typografi (heading-font, body-font)
- Voice i copy (rubriker, frågor, knappar, error-meddelanden)
- Logo-placering (top-left, default)

Defaults om brand-guidelines saknas:
- Färg: GrowthStackr-defaults (coral #FF6B6B accent, cream bg, near-black text)
- Typografi: Inter body, Instrument Serif italic accent
- Voice: direkt, konkret, ingen hype

### Steg 6: Bygg HTML-filen

Start från template i `references/templates/[format].html`. Templates är minimal — tom struktur + inline-styles + JavaScript-skelett. Fyll i:

- Title + meta-tags
- Frågor / input-fält
- Scoring / formel-logik
- Resultat-rendering
- Email-capture + submit
- CRM-webhook

Total filstorlek: under 50KB single-file HTML. Inga externa beroenden förutom 1 CDN-font.

### Steg 7: Test lokalt + Vercel-deploy

Test lokalt:
1. Öppna filen i browser
2. Fyll i alla frågor / input
3. Verifiera scoring/formel
4. Lämna email
5. Verifiera att webhook eller MCP-call går igenom (logga payload i console)

Deploy till Vercel:
```bash
vercel deploy --prod
```
Eller via Claude Code: `claude code "deploya denna fil till Vercel"`.

### Steg 8: Verifiera CRM-trigger

När leadet fyller i:
- Email landar i CRM som ny kontakt eller uppdaterad kontakt
- Custom fields (score, lead_magnet, submitted_at) fyller i på kontakten
- Rätt automation-sekvens triggas (TOFU/MOFU/BOFU baserat på lead_magnet-värdet)

Om CRM inte tar emot: kolla webhook-URL, CORS, payload-format.

## Output-format

1. **HTML-fil:** `lead-magnet-[id].html` i projekt-roten eller `/lead-magnets/`
2. **Deploy-instruktion:** kort markdown med 3 steg (öppna fil → vercel deploy → koppla domän)
3. **CRM-test-instruktion:** hur användaren verifierar att leads landar rätt
4. **Länk att lägga in på hemsidan:** path som L3.1-hemsidan ska länka till

## Failure conditions

Lever inte filen om:

- HTML:n inte renderar korrekt i Chrome / Safari
- Email-fältet saknar validering (måste vara giltig email-format)
- CRM-webhook eller MCP-call inte är inkopplad
- Filstorlek över 100KB (för många externa beroenden)
- Brand inte applicerad (default-styles syns)
- Resultatet inte är skräddarsytt baserat på svaren — om alla får samma output är det inte en interaktiv lead magnet, det är en form

## Anti-patterns

- "Submit form → tack-sida → ingen output" — då är det inte en lead magnet, det är en kontaktform
- "PDF-download efter ifyllning" — döda 2015-format, byt till interaktiv resultat-sida
- "Generic resultat ('Tack för dina svar!')" — outputen ska vara specifik baserat på input
- "Email skickas men inget pushas till CRM" — då hamnar leadet i en silo, hela poängen försvinner
- "20 frågor lång quiz" — completion-rate dyker, max 10 frågor

## References

- `references/templates/kalkylator.html` — minimal HTML-template för kalkylator (input-fält + formel-skelett + resultat-block)
- `references/templates/quiz.html` — minimal HTML-template för quiz (frågor + scoring + kategori-output)
- `references/templates/assessment.html` — minimal HTML-template för multistep-assessment (steg + personlig rapport)
- `references/integrations/hubspot-webhook.md` — webhook-format + custom field-mapping för HubSpot
- `references/integrations/highlevel-webhook.md` — samma för HighLevel

## Brand-inheritance

Skill:en läser alltid `brand-guidelines.skill.md` från Project innan den bygger. Om filen saknas — använd GrowthStackr-defaults men flagga till användaren att brand bör definieras i Modul 2 först.
