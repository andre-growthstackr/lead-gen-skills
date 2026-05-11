# Email HTML-template — GrowthStackr-branded

Inline-CSS-template för veckans newsletter. Klistras direkt in i ESP (HighLevel/MailChimp/HubSpot). Alla styles är inline för email-klient-kompatibilitet (Outlook, Gmail, Apple Mail, mobile).

## Variabler som ersätts av skillen

- `{{WEEK_NUMBER}}` — veckonummer
- `{{HERO_HEADLINE}}` — veckans rubrik
- `{{HERO_SUBHEAD}}` — preheader-stil intro
- `{{INTRO_BODY}}` — intro-stycke 80-120 ord
- `{{NEWS_N_TITLE}}`, `{{NEWS_N_SUMMARY}}`, `{{NEWS_N_TAKE}}`, `{{NEWS_N_URL}}` — per nyhet (1-5)
- `{{OUTRO_BODY}}` — outro 50-80 ord
- `{{CTA_URL}}`, `{{CTA_TEXT}}` — primary CTA-knapp
- `{{UNSUBSCRIBE_URL}}` — auto från ESP
- Brand-färger ersätts från `brand-guidelines.skill.md`:
  - `{{COLOR_PRIMARY}}` — t.ex. mörk text
  - `{{COLOR_ACCENT}}` — accent (knappar, länkar)
  - `{{COLOR_BG}}` — bakgrund
  - `{{COLOR_CARD}}` — nyhets-card bakgrund
  - `{{FONT_DISPLAY}}` — display-font
  - `{{FONT_BODY}}` — body-font

## Template

```html
<!DOCTYPE html>
<html lang="sv">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Veckans Marketing OS — vecka {{WEEK_NUMBER}}</title>
</head>
<body style="margin:0;padding:0;background-color:{{COLOR_BG}};font-family:{{FONT_BODY}},Helvetica,Arial,sans-serif;color:{{COLOR_PRIMARY}};">

<!-- Outer wrapper -->
<table width="100%" cellpadding="0" cellspacing="0" border="0" style="background-color:{{COLOR_BG}};">
<tr><td align="center">

<!-- Container -->
<table width="600" cellpadding="0" cellspacing="0" border="0" style="max-width:600px;background-color:{{COLOR_BG}};">

  <!-- Header -->
  <tr>
    <td style="padding:32px 24px 16px 24px;text-align:left;">
      <p style="margin:0;font-size:12px;letter-spacing:1.5px;text-transform:uppercase;color:{{COLOR_PRIMARY}};opacity:0.6;">
        Vecka {{WEEK_NUMBER}} · Marketing OS
      </p>
    </td>
  </tr>

  <!-- Hero -->
  <tr>
    <td style="padding:8px 24px 16px 24px;">
      <h1 style="margin:0 0 12px 0;font-family:{{FONT_DISPLAY}},Georgia,serif;font-size:38px;line-height:1.15;font-weight:400;font-style:italic;color:{{COLOR_PRIMARY}};">
        {{HERO_HEADLINE}}
      </h1>
      <p style="margin:0;font-size:16px;line-height:1.5;color:{{COLOR_PRIMARY}};opacity:0.75;">
        {{HERO_SUBHEAD}}
      </p>
    </td>
  </tr>

  <!-- Intro -->
  <tr>
    <td style="padding:16px 24px 24px 24px;">
      <p style="margin:0;font-size:16px;line-height:1.6;color:{{COLOR_PRIMARY}};">
        {{INTRO_BODY}}
      </p>
    </td>
  </tr>

  <!-- Divider -->
  <tr>
    <td style="padding:0 24px;">
      <hr style="border:0;border-top:1px solid {{COLOR_PRIMARY}};opacity:0.1;margin:0;">
    </td>
  </tr>

  <!-- News card 1 -->
  <tr>
    <td style="padding:24px;">
      <table width="100%" cellpadding="0" cellspacing="0" border="0" style="background-color:{{COLOR_CARD}};border-radius:8px;">
        <tr><td style="padding:20px 24px;">
          <p style="margin:0 0 4px 0;font-size:11px;letter-spacing:1px;text-transform:uppercase;color:{{COLOR_ACCENT}};font-weight:600;">
            Nyhet 1
          </p>
          <h2 style="margin:0 0 12px 0;font-size:20px;line-height:1.3;font-weight:700;color:{{COLOR_PRIMARY}};">
            {{NEWS_1_TITLE}}
          </h2>
          <p style="margin:0 0 12px 0;font-size:15px;line-height:1.55;color:{{COLOR_PRIMARY}};opacity:0.85;">
            {{NEWS_1_SUMMARY}}
          </p>
          <p style="margin:0 0 16px 0;font-size:15px;line-height:1.55;color:{{COLOR_PRIMARY}};border-left:3px solid {{COLOR_ACCENT}};padding-left:12px;font-style:italic;">
            <strong style="font-style:normal;">Andrés take:</strong> {{NEWS_1_TAKE}}
          </p>
          <a href="{{NEWS_1_URL}}" style="display:inline-block;font-size:14px;color:{{COLOR_ACCENT}};text-decoration:none;font-weight:600;border-bottom:1px solid {{COLOR_ACCENT}};">
            Läs hela artikeln →
          </a>
        </td></tr>
      </table>
    </td>
  </tr>

  <!-- News card 2 — copy structure from card 1, replace 1→2 -->
  <tr><td style="padding:0 24px 24px 24px;">
    <table width="100%" cellpadding="0" cellspacing="0" border="0" style="background-color:{{COLOR_CARD}};border-radius:8px;">
      <tr><td style="padding:20px 24px;">
        <p style="margin:0 0 4px 0;font-size:11px;letter-spacing:1px;text-transform:uppercase;color:{{COLOR_ACCENT}};font-weight:600;">Nyhet 2</p>
        <h2 style="margin:0 0 12px 0;font-size:20px;line-height:1.3;font-weight:700;color:{{COLOR_PRIMARY}};">{{NEWS_2_TITLE}}</h2>
        <p style="margin:0 0 12px 0;font-size:15px;line-height:1.55;color:{{COLOR_PRIMARY}};opacity:0.85;">{{NEWS_2_SUMMARY}}</p>
        <p style="margin:0 0 16px 0;font-size:15px;line-height:1.55;color:{{COLOR_PRIMARY}};border-left:3px solid {{COLOR_ACCENT}};padding-left:12px;font-style:italic;"><strong style="font-style:normal;">Andrés take:</strong> {{NEWS_2_TAKE}}</p>
        <a href="{{NEWS_2_URL}}" style="display:inline-block;font-size:14px;color:{{COLOR_ACCENT}};text-decoration:none;font-weight:600;border-bottom:1px solid {{COLOR_ACCENT}};">Läs hela artikeln →</a>
      </td></tr>
    </table>
  </td></tr>

  <!-- News card 3, 4, 5 — repeat same structure with index 3, 4, 5 -->
  <!-- (template engine duplicates this block) -->

  <!-- Outro + CTA -->
  <tr>
    <td style="padding:8px 24px 32px 24px;">
      <p style="margin:0 0 24px 0;font-size:16px;line-height:1.6;color:{{COLOR_PRIMARY}};">
        {{OUTRO_BODY}}
      </p>
      <table cellpadding="0" cellspacing="0" border="0">
        <tr><td style="background-color:{{COLOR_ACCENT}};border-radius:6px;">
          <a href="{{CTA_URL}}" style="display:inline-block;padding:14px 28px;font-size:15px;color:#ffffff;text-decoration:none;font-weight:600;">
            {{CTA_TEXT}}
          </a>
        </td></tr>
      </table>
    </td>
  </tr>

  <!-- Signature -->
  <tr>
    <td style="padding:0 24px 32px 24px;">
      <p style="margin:0;font-size:14px;line-height:1.5;color:{{COLOR_PRIMARY}};opacity:0.7;">
        André Lundqvist<br>
        GrowthStackr
      </p>
    </td>
  </tr>

  <!-- Footer -->
  <tr>
    <td style="padding:24px;border-top:1px solid {{COLOR_PRIMARY}}20;">
      <p style="margin:0 0 8px 0;font-size:12px;line-height:1.5;color:{{COLOR_PRIMARY}};opacity:0.5;text-align:center;">
        GrowthStackr AB · Stockholm, Sverige
      </p>
      <p style="margin:0;font-size:12px;line-height:1.5;color:{{COLOR_PRIMARY}};opacity:0.5;text-align:center;">
        Vill du inte ha veckans nyheter? <a href="{{UNSUBSCRIBE_URL}}" style="color:{{COLOR_PRIMARY}};opacity:0.7;text-decoration:underline;">Avregistrera</a>
      </p>
    </td>
  </tr>

</table>

</td></tr>
</table>

</body>
</html>
```

## Anpassningar per ESP

### HighLevel

- Bryt template i sektioner och lägg in via HighLevel-builderns "Custom HTML"-block
- Use HighLevels merge-fält för avregistrera-länken (auto-renderad)
- Test-send till dig själv innan draft sparas

### MailChimp

- Klistra hela templaten in i "Code your own / Paste in code"
- Lägg till `*|UNSUB|*` merge-tag som ersätter `{{UNSUBSCRIBE_URL}}`
- Run Inbox Preview innan save

### HubSpot

- Klistra in i Email Editor → Source Code-view
- Lägg till `{{ unsubscribe_link }}` HubL-tag istället för `{{UNSUBSCRIBE_URL}}`
- Test-send till dig själv

## Mobile-test

Template är responsiv via:

- Fixed 600px container med max-width
- All padding i px, inte %
- Font-sizes ≥ 14px för läsbarhet på mobil
- Knapp ≥ 44×44px touch-target

Test alltid i:

- Gmail web + Gmail app (iOS, Android)
- Apple Mail (macOS, iOS)
- Outlook (Windows + web)
- Litmus eller Email on Acid om budget finns

## Accessibility

- Alla länkar har tydlig text (inte "klicka här")
- Färgkontrast: minst 4.5:1 mellan text och bakgrund — verifiera mot brand-färgerna
- Alt-text på bilder om bilder används (templaten är text-only by default)
- Semantisk HTML — h1, h2 i rätt ordning

## Test-checklista innan ESP-save

- [ ] Alla {{VARIABLER}} ersatta
- [ ] Inga brutna länkar (klicka igenom alla)
- [ ] Inline-CSS hela vägen (ingen `<style>`-tag)
- [ ] Avregistrera-länken renderar med ESP-merge-tag
- [ ] Test-send till dig själv har gått igenom alla 3 stora klienter
- [ ] Mobile-render är läsbar
