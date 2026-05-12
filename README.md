# Claude för leadsgenerering — Skills

Ett komplett bibliotek av **Claude Skills** för B2B/B2C-marknadsföring, byggt för kursen *Claude för leadsgenerering*. **28 Skills** som täcker hela pipelinen från ICP-research till annonsering, organiserade som filer på din dator som ärvs av allt arbete — det här är ett *Growth OS*, inte en chatkonversation.

## Overview

| Modul | Skills | Tema |
|---|---|---|
| **Modul 1 — Foundation** | 4 | Growth OS-filosofi, prompt engineering grunder |
| **Modul 2 — Market Intelligence** | 5 | ICP, Voice of Customer, konkurrentanalys, varumärkespositionering |
| **Modul 3 — Webb, CRM & Lead Magnets** | 4 | Lead magnets, landningssidor, CRM-pipeline |
| **Modul 4 — Content** | 5 | Content-strategi, video, image posts, newsletter, Airtable-bas |
| **Modul 5 — Outbound** | 4 | Lead-listor, scoring, LinkedIn outreach, cold email |
| **Modul 6 — Ads** | 4 | Annonskoncept, Meta Ads, LinkedIn Ads, analytics |
| **Cross-cutting** | 2 | Higgsfield-prompts, NotebookLM-workflow |
| **Totalt** | **28** | |

Alla Skills är `SKILL.md`-filer med YAML frontmatter + workflow-instruktioner. Vissa har `references/`-mappar med detaljerade resurser (prompt-templates, HTML-mallar, MCP-setup-guider).

---

## Modul 1 — Foundation

Grunden allt annat står på: Growth OS-filosofin, prompt-arkitektur, klargörande frågor istället för gissningar.

| Skill | Beskrivning |
|---|---|
| `growth-os-philosophy` | Quick-reference för Growth OS-modellen: filer på datorn (icp-spec.md, brand.md, m.fl.) som ärvs av alla prompts. Förklarar skillnaden ad-hoc-prompting vs systemtänk. |
| `prompt-template-rmuk` | Roll/Mål/Uppgift/Kontext/Output-format-mall i markdown. Återanvändbar struktur för alla seriösa prompts. |
| `reverse-prompting` | Claude ställer 3-5 klargörande frågor en i taget tills 95% säker, innan generering. Slut på generic output från diffusa briefer. |
| `prompt-contracts` | Definition of Done + failure conditions per prompt. När Claude ska säga ifrån istället för att producera dålig output. |

---

## Modul 2 — Market Intelligence

Idealkund, kundröst, konkurrenter och varumärke som filer som driver allt downstream-arbete.

| Skill | Beskrivning |
|---|---|
| `icp-interview` | 12-fråge-interview för ICP-research, en fråga i taget. Scrapar din hemsida via Firecrawl-MCP. Output: `icp-spec.md` med firmographics, tech stack, mätbar smärta, köpfönster + TAM/SAM/dagligt lead-mål. |
| `voc-research` | Voice of Customer-mining från G2/Capterra/Trustpilot/Reddit/YouTube via Firecrawl. Volymregel ≥100 datapunkter. Output: `voc-rapport.md` med topp-5 pains + ordagranna citat + `voice-of-customer.skill.md` för återanvändning. |
| `brand-voice-interview` | 8-fråge-interview + hemside-scrape + konkurrent-kontrast. Output: `brand-guidelines.md` (5 sektioner) + `brand-guidelines.skill.md` (kortfattad version för återanvändning i alla framtida copy-jobb). |
| `konkurrentanalys` | Skrapar 3-5 konkurrenter (hemsida + Meta Ads Library + LinkedIn) via Firecrawl. Bygger `konkurrent-matris.md` med explicit "Lucka"-sektion — där pengarna finns. Kan köras schemalagt för vecko-diff. |
| `multi-agent-consensus` | Orkestrerar 3 parallella sub-agents (Strateg, Pragmatiker, Devil's Advocate) över 3 rundor för formuleringar med hög stake (brand voice, positioning, taglines, ad-headlines). |

---

## Modul 3 — Webb, CRM & Lead Magnets

Från lead magnet till CRM-pipeline — hela acquisition-funneln byggd och automatiserad.

| Skill | Beskrivning |
|---|---|
| `lead-magnet-ideation` | Läser `icp-spec.md` + `voc-rapport.md` och genererar 10-15 lead magnet-idéer fördelat över TOFU/MOFU/BOFU med format (quiz/kalkylator/assessment), pain och köpsignal per idé. |
| `lead-magnet-builder-interactive` | Bygger interaktiv HTML/JS-lead magnet (kalkylator/quiz/assessment) med email-capture, CRM-webhook och Vercel-deploy från en av tre templates. |
| `landing-page-builder` | Bygger single-file HTML/CSS-landningssida (lead magnet / webinar / sales) med italicerat coral-utfallsord i hero och pain-section driven av VoC. |
| `crm-pipeline-mcp` | Kör hela acquisition-funneln via HubSpot/GoHighLevel MCP — lead-intag, TOFU/MOFU/BOFU email+SMS-sekvenser, måndag-rapport på pipeline-status. |

---

## Modul 4 — Content

Content OS i Airtable + automatiserade pipelines för video, image posts och newsletter.

| Skill | Beskrivning |
|---|---|
| `airtable-content-os` | Schema-spec för Content OS-basen (Idéer → Content → Distribution) med fält, datatyper, relations och interface-vyer för veckogranskning. |
| `content-ideation` | Genererar 4 veckors content (28 pieces) fördelat 3 SFV + 2 image + 1 LFV + 1 newsletter per vecka. Mappar varje idé mot ICP-smärta + VoC-citat. Pushar till Airtable Content OS. |
| `short-form-video` | 7-stegs pipeline: idé från Content OS → script → shot-list → Hyperframe motion graphics som kod → B-roll-suggestions → komposit → push tillbaka till Content OS. 9:16 default, 15-30 sek. |
| `industry-news-monitoring` | Schemalagd fredagsroutine: Firecrawl + Google News → ranking 0-100 → topp-5 med din take → HTML email → ESP-draft (HighLevel/MailChimp/HubSpot). |

---

## Modul 5 — Outbound

Lead-listor, berikning, scoring, LinkedIn outreach och cold email — hela outbound-pipelinen.

| Skill | Beskrivning |
|---|---|
| `list-building` | ICP-driven käll-väljare (Apify/Google Maps/Apollo.io/Aleads) som scrapar 50-100 leads och importerar till Airtable Leads-tabell via MCP. |
| `lead-enrichment-scoring` | Sekvens diskvalificering → berikning (Apify + Firecrawl + APIs) → 0-100 scoring i fyra dimensioner med Claude som domare. Score > 70 markeras kvalificerad. |
| `linkedin-outreach-cowork` | Claude Cowork mot synligt Chrome, auto-research per lead via Activity-fliken, "referens + bro + CTA"-formel ≤300 tecken, max 25/dag, tvåstegs uppföljning. |
| `cold-email` | Sekundärdomän-setup, SPF/DKIM/DMARC, warmup 4-6 v, trigger-detektion, Instantly vs Claude Code/Cowork-väg, AI Appointment Setter med 30-sek auto-respond. |

---

## Modul 6 — Ads

Annonskoncept, Meta Ads, LinkedIn Ads och CFO-level analytics-rapportering.

| Skill | Beskrivning |
|---|---|
| `ad-ideation-4-angles` | Genererar 12 ad-koncept (4 vinklar × 3 hooks) från `icp-spec.md` + `voc-rapport.md`. Distribuerar till `short-form-video` och image-post-Skill för produktion. |
| `meta-ads-cli` | Publicerar Meta Lead Gen-kampanj programmatiskt via Meta Ads CLI med Custom Audience från CRM, 4 ad sets per vinkel, Auto-Research-loop vid CPA-stegring >20%. |
| `linkedin-ads` | LinkedIn Ads-publicering programmatiskt via LinkedIn Marketing API. Vinkel-mappning från `ad-ideation-4-angles`, 3-layer funnel (cold/warm/hot), Lookalike-expansion på Closed/Won-audience, CPL-cap. |
| `analytics-rapport` | Schemalagd CFO-rapport varje måndag 06:00 som frågar 5 MCPs parallellt (GA4 + Meta Ads + LinkedIn Ads + HubSpot + Stripe). Beräknar CAC/Payback/Kanal-bidrag. Genererar `.pptx` med tre paneler och mejlar. |

---

## Cross-cutting Skills

| Skill | Beskrivning |
|---|---|
| `higgsfield-prompts` | Six-delars prompt-template för 3D-animationer/Kling med exempel per use-case (hero-animation, ad-creative, image-post-bilder). Ärver brand-guidelines för stil. |
| `hyperframe-prompts` | Motion graphics som kod (SVG/Lottie). Workflow: filma rått → animera via prompt → bake into video. Ärver brand-typografi och animation-rytm. |
| `notebooklm-workflow` | Notebook per nisch + tre query-mönster (huvud-query, mottagar-query, datapunkt-query). Matar Growth OS-filerna med verifierad research från 30-50 källor per modul. |

---

## Architectural Pattern: Filer som ärvs

Skills i det här biblioteket bygger på en gemensam arkitektur:

```
~/.claude/ (eller motsvarande Project-rot)
├── icp-spec.md             ← genereras av icp-interview
├── voc-rapport.md          ← genereras av voc-research
├── konkurrent-matris.md    ← genereras av konkurrentanalys
├── brand-guidelines.md     ← genereras av brand-voice-interview
└── brand-guidelines.skill.md ← skill-version, ärvs av alla copy-jobb
```

När du senare kör en Skill som `lead-magnet-ideation`, `content-ideation`, `cold-email`, `ad-ideation-4-angles` eller `landing-page-builder`, läser den dessa filer automatiskt och ärver kontext. Du behöver aldrig briefa om din ICP, brand voice eller positionering igen.

**Det är skillnaden mellan ad-hoc-prompting och ett Growth OS.**

---

## Installation

Se [INSTALL.md](INSTALL.md) för full installations-guide (Claude Code, Claude Desktop, Claude.ai web).

Snabbstart (Claude Code):

```bash
git clone https://github.com/growthstackr/claude-leadsgenerering-skills.git
mkdir -p ~/.claude/skills
cp -r claude-leadsgenerering-skills/skills/* ~/.claude/skills/
claude
```

---

## Tillhörande resurser

- **Kursen** *Claude för leadsgenerering* — 26 lektioner i 7 moduler. Lektionsbeskrivningar, manus, slides och workbooks är canon för hur Skills i det här repot ska användas.
- **NotebookLM-source** — 104 verifierade källor (YouTube-videos, blogginlägg, dokumentation) som backar upp innehållet i Skills.

---

## Voice & Design

Alla Skills är skrivna på naturlig svenska med GrowthStackr-tonalitet:

- Korta meningar. Push-back. Konkret över abstrakt.
- Inga AI-tells, ingen hype, inga klyschor ("transformera", "synergier", "unlock", "revolutionerande", "10x", m.fl. förbjudna).
- Italicerade utfallsord i rubriker (coral-färg när visuellt).

---

## Bidra

Hittar du buggar, har förbättringsförslag eller saknar en Skill?

- Öppna en GitHub-issue
- Mejla support@growthstackr.io
- Skicka pull request

---

## License

[MIT License](LICENSE) — fritt att använda, modifiera och distribuera.

---

## Om GrowthStackr

GrowthStackr designar AI-drivna Growth OS för B2B/B2C-team som vill ersätta verktygskaos med ett system. Vi bygger custom Skills, MCPs och pipelines för kunder som inte vill vänta tre veckor på en byrå.

[growthstackr.io](https://growthstackr.io)

---

*Byggt för Claude Code av GrowthStackr.*
