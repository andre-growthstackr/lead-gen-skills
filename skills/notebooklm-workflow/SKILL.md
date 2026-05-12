---
name: notebooklm-workflow
description: Workflow för att använda NotebookLM som primär research-källa per nisch — skapa egen notebook, importera 30-50 källor, kör query-mönster (huvudquery, mottagar-query, datapunkt-query). Aktivera när användaren skriver "NotebookLM-workflow", "research per nisch", "skapa notebook för X", "queryar NotebookLM", "research innan generering", eller behöver veta hur dom samlar verklighet innan content-generering. Skillen beskriver hela research-loopen från notebook-setup till query-output som matar Growth OS-filerna.
---

# notebooklm-workflow

Quick-reference för NotebookLM som research-lager innan content-generering.

## Vad skillen gör

Beskriver exakt arbetsflöde för att använda NotebookLM som "verklighets-stage" före generering — så att Claude (och användaren) inte skriver content baserat på antaganden utan baserat på källor.

## Triggers

Aktivera när användaren skriver:

- "NotebookLM-workflow"
- "Research per nisch"
- "Skapa notebook för X"
- "Queryar NotebookLM"
- "Research innan generering"
- "Bygg en kunskapsbas för [bransch/segment]"
- "Hur ska jag research:a min ICP"

## Varför NotebookLM före generering

Direkt LLM-generering utan källor = gissning som låter rätt. NotebookLM ger:

- **Grounded output** — citat tillbaka till specifika källor
- **Repeterbarhet** — samma query mot samma källor ger samma kvalitet
- **Granskningsbarhet** — du kan kolla varifrån en datapunkt kom
- **Nisch-specifik kunskap** — generella LLMs vet inte om svenska SaaS-säljmöten 2026

## Workflow per nisch

### Steg 1: Skapa egen notebook

En notebook per ICP-segment eller per use-case. Inte en notebook för "allt".

Exempel:
- "B2B SaaS Sverige — content och positionering"
- "Outbound sales — kalla mejl och LinkedIn"
- "B2B-marknadsförare — pain research"

### Steg 2: Importera 30-50 källor

Sikta på 30-50 källor per notebook. Färre = för smal, fler = brus.

Källtyper:

- **YouTube-videos** (10-20 st) — talks, podcasts, demos från nyckelpersoner i nischen
- **Bloggar/artiklar** (10-15 st) — top-content från Substacks, företagsbloggar, branschmedier
- **Dokumentation** (5-10 st) — produktdokumentation, whitepapers, rapporter
- **Egen data** (5-10 st) — VoC-citat, kundintervjuer, sälj-anteckningar, support-tickets

Tumregel: kvalitet > kvantitet. 30 hand-kurerade källor slår 100 random.

### Steg 3: Tre query-mönster

NotebookLM används med tre query-typer beroende på vad du letar efter.

#### Huvudquery — bredd

Fångar landskapet inom ett ämne.

Exempel:
- "Vilka är de vanligaste smärtorna B2B-marknadsförare beskriver kring AI-implementering?"
- "Hur ramar tankeledare inom outbound in skillnaden mellan personalization och templating?"
- "Vilka content-format funkar bäst för svenska SaaS-företag enligt källorna?"

Output: 5-10 teman med citat. Använd för positionering, content-pillars, big-picture-förståelse.

#### Mottagar-query — djup per persona

Zooma in på en specifik roll/segment.

Exempel:
- "Vad säger Head of Marketing-personer specifikt om AI-pilots som flopar?"
- "Hur beskriver säljare (inte säljchefer) sin frustration med marketing-leads?"
- "Vad efterfrågar founders med <50 anställda när dom anlitar marknadsföringshjälp?"

Output: rolspecifika citat och språk. Använd för ICP-spec, copy som låter som mottagaren.

#### Datapunkt-query — konkret siffra/påstående

Hämtar specifika fakta att använda i content.

Exempel:
- "Vilka studier nämns kring AI-pilot success rate?"
- "Vad är typisk conversion rate för cold email i svenska B2B enligt källorna?"
- "Vilka exempel ges på personalization som faktiskt fungerade?"

Output: enskilda citat med källhänvisning. Använd för hooks, proof points, datapunkter i posts/mejl.

### Steg 4: Mata Growth OS-filerna

Output från NotebookLM ska aldrig hamna direkt i content. Det går först till Growth OS-filer:

- Huvudquery-output → positionering.md, voc-rapport.md
- Mottagar-query-output → icp-summary.md
- Datapunkt-query-output → voc-rapport.md (med citat och källa)

Sedan: när Claude genererar content, refererar promptens Kontext-sektion till Growth OS-filerna. Inte till NotebookLM direkt.

### Steg 5: Uppdatera, inte ersätt

NotebookLM-notebooks lever vidare. Lägg till nya källor löpande:

- Varje månad: 3-5 nya källor per aktiv notebook
- Efter kundintervju: lägg in transkript som källa
- När en bransch-rapport släpps: importera

Notebook där du inte längre lägger till källor är död.

## Output-format för en NotebookLM-research-session

När användaren ber Claude göra en NotebookLM-session, leverera:

```markdown
# Research-session: [ämne]

## Notebook
- Namn: [notebook-namn]
- Källor: [antal]

## Huvudquery
[Frågan]

### Findings
- Tema 1: [beskrivning]. Källa: [citat]
- Tema 2: [beskrivning]. Källa: [citat]
- ...

## Mottagar-query: [persona]
[Frågan]

### Findings
- ...

## Datapunkt-query: [vad]
[Frågan]

### Findings
- Datapunkt 1: [siffra/påstående]. Källa: [citat]
- ...

## Uppdatera Growth OS
- icp-summary.md: lägg till [vad]
- voc-rapport.md: lägg till citat [X, Y, Z]
- positionering.md: revidera sektion om [vad]
```

## Failure conditions

Säg ifrån om:

- Användaren har <10 källor i notebooken — för tunt för meningsfull query
- Användaren vill köra NotebookLM på en bred "samhälls"-fråga utan nischat fokus — fel verktyg, det blir generic
- Användaren vill att Claude ska generera content direkt från NotebookLM utan att gå via Growth OS-filer — bryter mot systemtänket
- Notebookens källor är mest från LLM-output (artiklar skrivna av AI om AI) — risk för hallucination-loop, kräv original-källor

## References

- `growth-os-philosophy` — NotebookLM matar Growth OS-filerna, ersätter dom inte
- `prompt-template-rmuk` — content-prompts refererar till Growth OS i Kontext-sektionen, inte direkt till NotebookLM
- `airtable-content-os` — Idéer-tabellens "Källa"-fält kan inkludera "NotebookLM"
