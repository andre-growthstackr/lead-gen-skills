# Master Skills

Reserverat för framtida orchestrator-Skills som routar förfrågningar till sub-Skills.

Just nu finns alla 28 Skills i `../skills/` som standalone Skills. När någon växer ut till en orchestrator (orchestrator-stil med routing table + sub-skills) flyttas den hit.

Exempel på framtida master Skills:
- `outbound-pipeline` — orchestrator för list-building → enrichment → outreach → cold-email
- `ads-pipeline` — orchestrator för ideation → meta → linkedin → analytics
- `content-engine` — orchestrator för ideation → video → image → newsletter

Bidra gärna med PR om du bygger en orchestrator.
