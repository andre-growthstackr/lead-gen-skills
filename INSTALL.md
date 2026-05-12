# Installation

Det här är **Claude Skills** — markdown-filer som Claude läser automatiskt när du triggar dom. Tre installationssätt beroende på vilken Claude-miljö du jobbar i.

## Claude Code (rekommenderat)

```bash
# Klona repot
git clone https://github.com/growthstackr/claude-leadsgenerering-skills.git
cd claude-leadsgenerering-skills

# Kopiera alla Skills till din lokala Claude Skills-mapp
mkdir -p ~/.claude/skills
cp -r skills/* ~/.claude/skills/
cp -r master-skills/* ~/.claude/skills/

# Starta Claude Code
claude
```

Verifiera att Skills är inlästa:

```
> /skills
```

Du ska se alla Skills i listan. Om Claude inte triggar en Skill automatiskt när du borde — skriv exakt trigger-frasen från Skills `description`-fält.

## Claude Desktop (för dom som inte kör Claude Code)

1. Öppna **Claude Desktop** → Settings → Skills
2. Klicka **+ New Skill** och välj **Import from folder**
3. Peka på `skills/<skill-name>` eller `master-skills/<skill-name>`-mappen
4. Upprepa för varje Skill du vill installera

Eller, installera selektivt — välj bara dom Skills som matchar var du är i kursen:

| Modul | Skills att installera först |
|---|---|
| Modul 1 (Foundation) | `growth-os-philosophy`, `prompt-template-rmuk`, `reverse-prompting`, `prompt-contracts` |
| Modul 2 (Market Intelligence) | `icp-builder`, `voc-research`, `brand-voice-interview`, `multi-agent-consensus`, `konkurrentanalys` |
| Modul 3 (Webb, CRM & Lead Magnets) | `lead-magnet-ideation`, `lead-magnet-builder-interactive`, `landing-page-builder`, `crm-pipeline-mcp` |
| Modul 4 (Content) | `content-ideation`, `short-form-video`, `image-post`, `industry-news-monitoring`, `airtable-content-os` |
| Modul 5 (Outbound) | `list-building`, `lead-enrichment-scoring`, `linkedin-outreach-cowork`, `cold-email` |
| Modul 6 (Ads) | `ad-ideation-4-angles`, `meta-ads-cli`, `linkedin-ads`, `analytics-rapport` |

## Claude.ai (web)

Web-versionen stödjer Skills via Projects. Workflow:

1. Skapa ett **Project** i Claude.ai för ditt företag (t.ex. "Growth OS — [företagsnamn]")
2. För varje Skill du vill använda:
   - Kopiera innehållet i `SKILL.md`
   - Klistra in i Project Instructions, eller
   - Skapa en separat "Skill"-fil i Projektets Files-sektion
3. Skills triggar när du skriver matchande trigger-fraser i en chat inom projektet

## Prerequisites

Dom här Skills förutsätter att du har:

- **Claude Pro eller Team-plan** (Free-plan saknar Skills och MCPs)
- **Anthropic-konto** med Skills aktiverat (rolls ut gradvis under 2026)

Vissa Skills kräver tilläggsverktyg:

| Skill | Kräver |
|---|---|
| `voc-research`, `konkurrentanalys`, `industry-news-monitoring` | Firecrawl MCP (firecrawl.dev) |
| `list-building` | Apify-konto + Apify MCP |
| `crm-pipeline-mcp` | HubSpot eller GoHighLevel MCP |
| `linkedin-outreach-cowork` | Claude Cowork (Chrome-extension) |
| `cold-email` | Egen sekundärdomän + SPF/DKIM/DMARC + Instantly-konto (eller motsvarande) |
| `meta-ads-cli` | Meta Ads CLI + Meta Business Manager-access |
| `linkedin-ads` | LinkedIn Campaign Manager + Insight Tag på sajten |
| `analytics-rapport` | GA4 + Meta Ads API + LinkedIn Ads API + HubSpot/HighLevel MCP + Stripe MCP |

Setup för varje verktyg dokumenteras i respektive Skills `references/`-mapp.

## Uppdatera Skills

När repot uppdateras:

```bash
cd claude-leadsgenerering-skills
git pull
cp -r skills/* ~/.claude/skills/
cp -r master-skills/* ~/.claude/skills/
```

## Felsökning

**Claude triggar inte Skill när jag förväntar mig det:**
Skriv exakt trigger-frasen från Skills `description`-fält. Eller invokera den manuellt: `Använd skill <skill-name> och gör X`.

**Skill genererar fel output-format:**
Kolla `## Output-format`-sektionen i SKILL.md — där står exakt vad Skillen ska producera. Om Claude avviker, klistra in den sektionen i samma chat som krav.

**Skill kraschar mot ett verktyg:**
Kolla `## Failure conditions`-sektionen. Verktyget kan vara icke-autentiserat eller offline.

---

Frågor? Issues? Öppna en GitHub-issue eller mejla support@growthstackr.io.
