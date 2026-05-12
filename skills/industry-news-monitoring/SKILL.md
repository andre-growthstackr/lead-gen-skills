---
name: industry-news-monitoring
description: Schemalagd veckovis nyhetsbevakning för newsletter. Skrapar 10 mest delade nyheter i ICP:s nisch via Firecrawl + Google News varje fredag morgon, rankar efter viral-potential, plockar topp-5 och genererar newsletter-outline med Andrés vinkel per nyhet. Renderar som GrowthStackr-branded HTML email och pushar till HighLevel/MailChimp/HubSpot via MCP. Använd den varje gång användaren skriver "kör news monitoring", "skapa veckans newsletter", "fredagsnyheter", eller hänvisar till Modul 4 L4.4. Använd även automatiskt på schema om scheduled-tasks är aktiverat. Denna skill ÄR newsletter-motorn i Content OS.
---

# industry-news-monitoring

Skill för Modul 4 lektion L4.4 — schemalagd nyhetsbevakning som matar veckans newsletter. Skrapar nyheter, rankar dem, genererar outline med Andrés vinkel, renderar HTML och pushar till ESP.

## Vad denna skill ÄR till för

- Schemalagd routine som körs varje fredag morgon (kan triggas manuellt)
- Skrapa 10 mest delade nyheter i ICP:s nisch via Firecrawl + Google News
- Ranka nyheterna efter viral-potential (delning, källa, recency)
- Plocka topp-5 + en kontrarian-take eller åsikt per nyhet
- Generera newsletter-outline: intro + 5 nyheter med Andrés vinkel + CTA
- Rendera som GrowthStackr-branded HTML email
- Pusha till HighLevel/MailChimp/HubSpot via MCP

## Vad denna skill INTE gör

- Generera content-idéer från VoC → använd `content-ideation`
- Skicka emailet — den lägger draft i ESP, André trycker Send
- Bygga ESP-listor eller segment — det är ESP-arbete
- Skriva SFV-scripts från nyheterna — det är `short-form-video` om André vill repurpa

## Triggers

Skillen aktiveras när användaren skriver:

- "kör news monitoring"
- "skapa veckans newsletter"
- "fredagsnyheter"
- "newsletter [vecka]"
- Hänvisning till Modul 4 / L4.4

Kan också triggas automatiskt via `scheduled-tasks` varje fredag kl 06:00.

## Inputs som behövs innan du börjar

1. **ICP-nisch + sökord** — från `icp-summary.md`. Måste innehålla 5-10 sökord/fraser för Google News, plus 3-5 domäner att skrapa (TechCrunch, MarTech Today, etc).
2. **Brand guidelines** — från `brand-guidelines.skill.md` i Project. För email-template-färger, font, voice.
3. **Andrés voice-defaults** — direkt, push-back, naturlig svenska, inga AI-tells.
4. **ESP-credentials** — HighLevel/MailChimp/HubSpot via MCP. Om ingen är ansluten — generera HTML och låt André klistra in manuellt.

## Workflow

### Steg 1: Skrapa nyheter

För varje sökord/domän i ICP-konfigurationen:

1. **Google News-sökning** via Firecrawl `firecrawl_search` med tidsfilter senaste 7 dagar
2. **Domän-skrapning** via `firecrawl_scrape` för fasta källor (TechCrunch, MarTech, etc)
3. Samla minst 20-30 kandidat-nyheter

Output per nyhet: titel, URL, sammanfattning (max 100 ord), publiceringsdatum, källa.

### Steg 2: Ranka viral-potential

Varje nyhet får ett poäng 0-100 baserat på:

| Faktor | Vikt | Hur det mäts |
|---|---|---|
| Delningar / engagement | 40% | LinkedIn shares, Twitter shares (manuell eller via API) |
| Källans auktoritet | 25% | TechCrunch > Medium-blogg. Domain authority från Ahrefs eller manuell rating |
| Recency | 20% | Senaste 24h = full pott, äldre = decay |
| Relevans för ICP | 15% | Match mot ICP-sökord — fler matches = högre |

Full scoring-detaljer + exempel i `references/ranking-criteria.md`.

Sortera nyheterna efter poäng. Plocka topp-5.

### Steg 3: Generera Andrés vinkel per nyhet

För varje topp-5-nyhet — generera en "Andrés take" som:

- **Är konkret** — inte "intressant utveckling"
- **Har push-back där det är förtjänat** — "Det här är ännu en AI-hype-cykel" / "Det här är faktiskt värt att kolla"
- **Är 2-4 meningar** — inte ett helt blogginlägg
- **Refererar till ICP-smärta** — vad betyder detta konkret för en B2B-marknadsförare
- **Slutar med en åsikt eller en uppmaning** — inte "vi får se hur det utvecklas"

Exempel:

```
Nyhet: "OpenAI släpper agent-mode för enterprise"
Andrés take: "Tredje agent-launchen i år. De första två gav noll ROI för 95% av piloterna.
Det här blir samma — om du inte börjar med en konkret use-case som du redan har 
mätbar pipeline-impact på. Skippa hype, börja med en process du gör 10 ggr i veckan."
```

### Steg 4: Generera newsletter-outline

Format:

```markdown
# Veckans Marketing OS — vecka [N]

## Subject line
[5 alternativ — A/B-testa]

## Preheader
[max 100 tecken]

## Intro (80-120 ord)
[Veckans hook + Andrés personliga vinkel på veckan]

## Nyhet 1: [Titel]
- **Vad:** [2-3 meningar sammanfattning]
- **Andrés take:** [2-4 meningar]
- **Länk:** [URL]

## Nyhet 2-5: [...]

## Outro / CTA (50-80 ord)
[Vad gör läsaren härnäst — kommentera, svara på emailet, klicka]
```

### Steg 5: Rendera som HTML email

Om brand guidelines finns — använd template i `references/email-html-template.md`.

Template har:

- Hero-rubrik med veckans tema
- 5 nyhets-cards med titel + sammanfattning + Andrés take + CTA-knapp
- Outro med André-signatur + sociala länkar
- Footer med avregistrera + företagsinfo

Rendera med inline-CSS (email-klient-säkert) och testa i Litmus/Email on Acid om tillgängligt.

### Steg 6: Push till ESP

Beroende på vilken ESP som är ansluten:

**HighLevel via MCP:**
1. Skapa email-template i HighLevel-kontot
2. Sätt subject + preheader + HTML-body
3. Status: Draft (André granskar och skickar)

**MailChimp via MCP:**
1. Skapa campaign med template
2. Sätt subject + from-name + reply-to
3. Status: Draft

**HubSpot via MCP:**
1. Skapa marketing email
2. Sätt subject + body + recipient list
3. Status: Draft

Om ingen ESP är ansluten — leverera HTML som fil + instruktioner för manuell paste.

## Output-format

### Leverans-paket per vecka

```
# Newsletter: Vecka [N] ([datum])

## Nyhets-research (ranking-table)
[20-30 kandidater med poäng, sortabel]

## Topp-5 (gick vidare till outline)
[5 nyheter med Andrés take]

## Newsletter-outline
[markdown enligt format ovan]

## HTML-render
[fil eller länk till ESP-draft]

## ESP-status
- Draft skapad i [ESP] med ID [N]
- Subject A/B-test: [valt subject]
- Schemalagd send: [tid eller "väntar på André"]
```

## Failure conditions

Skillen ska INTE leverera om:

- Firecrawl returnerar < 10 kandidater — för lite material, gå tillbaka och utöka sökord
- Brand guidelines saknas — risk för fel template-färger, fel voice
- ICP-sökord saknas eller är för breda ("B2B", "marketing") — fråga André om bättre sökord
- ESP-MCP är instabil — leverera HTML-fil istället för att försöka pusha
- Topp-5-nyheterna är från samma källa eller samma vinkel — diversifiera, gå djupare i listan

## References

- `references/ranking-criteria.md` — viral-potential-scoring (delning-vikt, källa-vikt, recency-vikt) med exempel-beräkningar
- `references/email-html-template.md` — GrowthStackr-branded email-HTML template med inline-CSS, klar för ESP-paste

## Self-check (obligatorisk innan leverans)

- [ ] 20-30 kandidat-nyheter skrapade
- [ ] Varje nyhet rankad med poäng 0-100
- [ ] Topp-5 plockade från olika källor (max 2 från samma källa)
- [ ] Varje topp-5 har Andrés take på 2-4 meningar
- [ ] Push-back finns i minst 2 av 5 takes
- [ ] Subject line genererad i 5 alternativ
- [ ] Intro 80-120 ord
- [ ] Outro 50-80 ord med konkret CTA
- [ ] HTML-render testad i email-preview (inga brutna länkar, inline-CSS)
- [ ] Brand-färger + font matchar guidelines
- [ ] Inga AI-tells i takes eller intro
- [ ] Inga förbjudna ord
- [ ] Draft pushad till ESP ELLER HTML-fil levererad
