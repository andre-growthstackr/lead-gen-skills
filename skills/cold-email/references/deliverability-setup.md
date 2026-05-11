# Deliverability-setup: SPF/DKIM/DMARC step-by-step

Stegvis konfiguration för sekundärdomän så cold-email faktiskt landar i inkorgen, inte i spam. Allt sker på sekundärdomänen — aldrig på huvuddomänen.

## Förutsättningar

- Sekundärdomän registrerad och pekande till din DNS-provider
- 301-redirect från sekundärdomän till huvuddomän (för trovärdighet)
- Email-konton skapade på sekundärdomänen via Google Workspace eller Microsoft 365

## Steg 1: SPF (Sender Policy Framework)

SPF talar om vilka servrar som får skicka email i sekundärdomänens namn.

### DNS-record

Typ: `TXT`
Namn: `@` (rot-domänen)
Värde (exempel för Google Workspace):
```
v=spf1 include:_spf.google.com ~all
```

För Microsoft 365:
```
v=spf1 include:spf.protection.outlook.com ~all
```

För Instantly (om de skickar i ditt namn):
```
v=spf1 include:_spf.google.com include:instantly-spf.com ~all
```

### Verifiera

Kör i terminal:
```bash
dig TXT sekundärdomän.com +short
```

Eller använd `mxtoolbox.com/spf.aspx`.

## Steg 2: DKIM (DomainKeys Identified Mail)

DKIM signerar varje email med en privat nyckel så mottagaren kan verifiera att den inte manipulerats.

### Google Workspace

1. Google Admin → Apps → Google Workspace → Gmail → Authenticate email
2. Välj sekundärdomän
3. Klicka "Generate new record" (välj 2048-bit)
4. Kopiera DNS-record som genereras

### DNS-record

Typ: `TXT`
Namn: `google._domainkey` (Google) eller `selector1._domainkey` (Microsoft)
Värde: lång string som börjar med `v=DKIM1; k=rsa; p=...`

### Verifiera

```bash
dig TXT google._domainkey.sekundärdomän.com +short
```

Eller `mxtoolbox.com/dkim.aspx`.

Aktivera signering i Google Admin efter DNS-propagering (kan ta 24h).

## Steg 3: DMARC (Domain-based Message Authentication)

DMARC kombinerar SPF + DKIM och säger till mottagaren vad de ska göra om email-et inte verifieras.

### DNS-record (start lugnt)

Typ: `TXT`
Namn: `_dmarc`
Värde (för start):
```
v=DMARC1; p=none; rua=mailto:dmarc@sekundärdomän.com; pct=100
```

`p=none` = bara rapportera, blockera inte (säker start).

### Eskalera över tid

Vecka 1-2: `p=none` (samla data)
Vecka 3-4: `p=quarantine; pct=25` (25% till spam om fail)
Vecka 5+: `p=quarantine; pct=100` (allt till spam om fail)

Gå INTE till `p=reject` på sekundärdomän — det är överkill för cold-email och kan blockera legitima retur-emails.

### Verifiera

```bash
dig TXT _dmarc.sekundärdomän.com +short
```

## Steg 4: Sätt upp rDNS (Reverse DNS) om möjligt

Vissa providers (inte alla) tillåter rDNS-konfiguration. Om din provider tillåter:

- rDNS ska peka från din sending-IP tillbaka till sekundärdomänen
- Höjer deliverability ~5%

För Google Workspace och Microsoft 365 hanteras detta automatiskt av providern.

## Steg 5: Verifiera helheten

Skicka testemail från sekundärdomänen till:

1. `check-auth@verifier.port25.com` — får tillbaka rapport
2. `mail-tester.com` (skicka till deras unika adress)

Båda ska visa SPF: pass, DKIM: pass, DMARC: pass.

Mål-score på mail-tester: 9/10 eller högre. Under 8 → kör om setup.

## Steg 6: Warmup innan riktig volym

Aldrig hoppa direkt till 30 email/dag på ny inbox.

Warmup-schema (per inbox):

| Vecka | Volym/dag | Strategi |
|---|---|---|
| 1 | 5 | Mest mellan dina egna konton för engagement |
| 2 | 10 | Lägg till några varma kontakter |
| 3 | 15 | Börja blanda in cold-kontakter |
| 4 | 25 | Mest cold, fortfarande engagement-loops |
| 5 | 35 | Cold-volym, monitorera reputation |
| 6+ | 45-50 | Full produktion |

Använd Instantly warmup eller Mailwarm för automatiserad warmup-volym.

## Tecken på problem

- Bounce-rate > 5% → email-verifiering är sönder, fix i L5.2
- Reply-rate < 2% efter 100 emails → copy är problem, inte deliverability
- Reply-rate dippar plötsligt → blacklist-check via `mxtoolbox.com/blacklists.aspx`
- Spam-complaints > 0.1% → pausa, omvärdera ICP-match

## Anti-patterns

- Skicka cold-volym från huvuddomän (6-12 mån reputation-skada om det går snett)
- Hoppa över warmup ("jag har bråttom")
- `p=reject` på DMARC från start (blockerar legitima retur-emails)
- Använda samma sekundärdomän för flera produkter/varumärken (förvirring + reputation-mix)
- Sätta upp 10 sekundärdomäner samtidigt — start med en, lär dig, skala
