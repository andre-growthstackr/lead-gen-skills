---
name: growth-os-philosophy
description: Quick-reference för Growth OS-filosofin — filer på datorn som ärvs av alla prompts, inte ad-hoc chats. Aktivera när användaren frågar "vad är Growth OS", "varför filer på datorn", "ad-hoc vs system", "skillnad mellan Skills och Projects", "varför inte bara prompts", eller hänvisar till portabilitet mellan Claude/ChatGPT/Cursor. Skillen förklarar vad Growth OS är, vilka kärnfiler som ingår, hur Skills/Projects/MCPs samarbetar, och varför filer slår chats för långsiktigt arbete.
---

# growth-os-philosophy

Kunskapsbas för vad Growth OS är och varför kursen bygger på filer istället för ad-hoc-promptning.

## Vad skillen gör

Förklarar Growth OS-modellen på ett konkret sätt när användaren frågar. Detta är ingen workflow-skill — den är en quick-reference som Claude refererar till för att svara konsekvent på "varför gör vi så här".

## Triggers

Aktivera när användaren skriver eller frågar om:

- "Vad är Growth OS"
- "Varför filer på datorn"
- "Ad-hoc prompting vs system"
- "Skillnad mellan Skills, Projects och MCPs"
- "Varför inte bara använda chats"
- "Hur funkar portabilitet mellan Claude och andra verktyg"
- "Vad ska ligga i Growth OS-mappen"

## Definitionen av Growth OS

Growth OS = en uppsättning markdown-filer på datorn som beskriver verksamheten (ICP, brand, positionering, pipelines, VoC) och som varje prompt ärver automatiskt.

Tre egenskaper:

1. **Persistent** — finns kvar mellan sessioner, dör inte med en chat
2. **Verktygs-portabelt** — samma filer fungerar i Claude.ai, Claude Code, Cursor, ChatGPT
3. **Versionsbart** — kan committas till git, granskas, rullas tillbaka

## Skillnad: ad-hoc vs system

**Ad-hoc-prompting** = du skriver en prompt, får output, kopierar någonstans, prompten dör. Nästa gång börjar du om — Claude vet inget om din ICP, ditt brand, dina pipelines.

**Systemtänk** = du beskriver verksamheten en gång i filer. Varje prompt refererar till dem. Output blir konsekvent oavsett vem som promptar eller vilket verktyg som används.

Ad-hoc fungerar för engångsuppgifter. Allt återkommande arbete (innehåll, sales, lead gen) kräver system.

## Kärnfiler i Growth OS

Minsta uppsättning för B2B-marknadsförare:

- **icp-spec.md** — Vem köper. Smärtor, triggers, jobs-to-be-done, motargument, vokabulär.
- **brand-guidelines.skill.md** — Tonalitet, förbjudna ord, exempel-meningar, stilregler.
- **positionering.md** — Vad ni säger om er själva. Category, differentiering, proof points.
- **pipelines.md** — Hur leads flödar genom funneln. Stages, kvalifikation, handoffs.
- **voc-rapport.md** — Voice-of-customer. Faktiska citat från kundintervjuer som driver copy.

Lägg till per behov: produktspec, säljargument, FAQ, konkurrentanalys.

## Hur Skills, Projects och MCPs samarbetar

**Skills** = återanvändbara arbetsmoment. T.ex. "skriv LinkedIn-post i vår brand voice" eller "generera ICP-research-frågor". Skills ligger som markdown-filer och triggas av specifika fraser.

**Projects** = Claude.ai-funktion där du laddar upp Growth OS-filerna en gång. Alla chats i projektet ärver filerna automatiskt.

**MCPs** = kopplingar till externa system (Airtable, Gmail, Calendar). MCPs ger Claude tillgång till verklig data — Skills och Projects ger Claude tillgång till verksamhetens hjärna.

Stack: Growth OS-filer (hjärnan) → Skills (vanorna) → Projects (arbetsytan) → MCPs (händerna).

## Portabilitet — varför filer på datorn

Filerna är vanliga markdown. Det betyder:

- Byter du från Claude.ai till Cursor — samma filer fungerar
- Anlitar du en byrå — du skickar Growth OS-mappen, dom kör samma standard
- Ny medarbetare — onboarding = läs mappen
- Verktyget byter pris/policy — du flyttar utan att förlora arbete

Chats är låsta i ett verktyg. Filer är dina.

## Output-format

När användaren frågar något ovanstående: svara direkt, konkret, med exempel där det hjälper. Använd inte hela skillen som svar — välj den del som matchar frågan.

## Failure conditions

Säg ifrån om:

- Användaren ber om en "Growth OS-mall" som färdig fil — strukturen är personlig per verksamhet, ge inte generisk mall utan att veta ICP/brand först
- Användaren tror Skills ersätter Growth OS-filer — de kompletterar varandra, inte ersätter
- Användaren vill lägga sekret-data (lösenord, API-nycklar) i Growth OS — det hör hemma i en password manager, inte i markdown

## References

- `prompt-template-rmuk` — RMUK-mallen refererar till Growth OS-filer i sin Kontext-sektion
- `notebooklm-workflow` — NotebookLM är research-lagret som matar Growth OS-filerna med data
