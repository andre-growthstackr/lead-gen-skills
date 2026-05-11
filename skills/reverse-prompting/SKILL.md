---
name: reverse-prompting
description: Claude ställer 3-5 klargörande frågor (en i taget) innan generering, tills 95% säker på vad användaren vill ha. Aktivera när användaren skriver "kör reverse prompting", "ställ klargörande frågor först", "fråga mig innan du producerar", "reverse prompt det här", eller när uppgiften är så vag att en direkt generering skulle bli slöseri. Skillen stoppar när Claude har tillräckligt med info för att producera något användbart — inte tidigare.
---

# reverse-prompting

Skill för att vända prompten — Claude frågar användaren istället för att gissa.

## Vad skillen gör

Aktiverar ett frågeläge där Claude ställer 3-5 specifika frågor, en i taget, innan något genereras. Syftet är att fånga upp det användaren inte sagt — mottagare, tonalitet, längd, restriktioner, success criteria.

## Triggers

Aktivera när användaren skriver:

- "Kör reverse prompting"
- "Ställ klargörande frågor först"
- "Fråga mig innan du producerar"
- "Reverse prompt det här"
- "Jag vill att du frågar mig"

Aktivera även proaktivt (efter att ha sagt det till användaren) om:

- Uppgiften saknar mottagare ("skriv något om X" — för vem?)
- Tonalitet är oklar och varierar drastiskt beroende på kontext
- Längd/format är ospecificerat och spelar stor roll
- Det finns flera rimliga tolkningar av uppgiften

## Workflow

1. Bekräfta att reverse prompting startar: "Okej, jag ställer några frågor innan jag skriver."
2. Ställ **en fråga i taget**. Inte en lista. Vänta på svar.
3. Varje fråga ska vara specifik och stänga en faktisk osäkerhet — inte generic "vad är din målgrupp?"
4. Stoppvillkor: när du är 95% säker på vad du ska producera, säg det och börja generera
5. Max 5 frågor. Om du efter 5 frågor inte vet — säg det och föreslå en begränsad version

## Fråge-mall (gör om till uppgiften)

Bra frågor är specifika och har förslag inbäddade:

- "Vem är mottagaren — säljare som ska prospekta, eller marknadsförare som ska skriva content?"
- "Ska tonen vara push-back-direkt (kursens default) eller mjukare för en kall publik?"
- "Är det här för en post du skickar idag, eller en mall du återanvänder?"
- "Vilken Growth OS-fil ska den här ärva från — bara brand-guidelines eller också icp-spec?"
- "Ska outputen vara klar att klistra in, eller är det ett utkast vi itererar på?"

Dåliga frågor (undvik):

- "Berätta mer om vad du vill ha" — för generic
- "Vad är din målgrupp?" — för bred, ge förslag
- "Vad är ditt budskap?" — om du inte vet budskapet, kör inte reverse prompting på rätt nivå

## Stoppvillkor

Sluta fråga och börja generera när:

- Du vet mottagare
- Du vet format/längd
- Du vet tonalitet
- Du vet vilka Growth OS-filer som gäller
- Du vet hur "klart" ser ut (kladd eller färdigt)

Säg: "Okej, jag har vad jag behöver. Jag skriver nu — säg till om något inte landar."

## Failure conditions

Säg ifrån om:

- Användaren svarar genomgående "jag vet inte" — då är reverse prompting fel verktyg. Föreslå att börja med research eller exempel istället.
- Frågorna börjar bli filosofiska istället för operativa — du jagar definition när du borde generera ett utkast.
- Efter 5 frågor är uppgiften fortfarande oklar — leverera ett begränsat utkast med tydliga antaganden uppe i texten.

## References

- `prompt-template-rmuk` — efter reverse prompting kan du formalisera prompten som RMUK
- `prompt-contracts` — använd det Claude lärt sig i reverse prompting för att sätta Definition of Done
