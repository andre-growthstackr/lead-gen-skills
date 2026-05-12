# VoC Report — docx Generation Spec

Load this during Step 8 (delivery) along with `docx_api.md`. This document
defines exactly how every element in the VoC Output Template translates into
docx-js patterns.

Mirror the patterns established in `icp-builder` for visual consistency — the
two reports often live side-by-side in the same Project Knowledge folder.

---

## File metadata

```javascript
const doc = new Document({
  creator: "voice-of-customer skill",
  title: "Voice of Customer Report — " + companyName,
  description: "VoC research output",
  styles: { /* see styles section below */ },
  sections: [{ children: docChildren }]
});
```

---

## Styles — defaults

```javascript
styles: {
  default: {
    document: {
      run: { font: "Arial", size: 22, color: "000000" },  // 11pt black body
    },
  },
  paragraphStyles: [
    {
      id: "Heading1",
      name: "Heading 1",
      basedOn: "Normal",
      next: "Normal",
      quickFormat: true,
      run: { font: "Arial", size: 40, bold: true, color: "000000" },  // 20pt bold
      paragraph: { spacing: { before: 360, after: 200 } },
    },
    {
      id: "Heading2",
      name: "Heading 2",
      basedOn: "Normal",
      next: "Normal",
      quickFormat: true,
      run: { font: "Arial", size: 30, bold: true, color: "000000" },  // 15pt bold
      paragraph: { spacing: { before: 240, after: 120 } },
    },
    {
      id: "QuoteBody",
      name: "Quote Body",
      basedOn: "Normal",
      run: { italics: true, color: "404040" },  // grey-700
      paragraph: { indent: { left: 720 }, spacing: { after: 60 } },  // 0.5" indent
    },
    {
      id: "Attribution",
      name: "Attribution",
      basedOn: "Normal",
      run: { size: 18, color: "808080" },  // 9pt grey
      paragraph: { indent: { left: 720 }, spacing: { after: 240 } },
    },
  ],
}
```

---

## Callout boxes — `#fcc4b0` background

Two callout boxes appear in the VoC report:

1. **Volume & Methodology Note** — early in the doc, after the executive summary
2. **Surprise Insight** — near the end, before recommendations

Pattern (same as icp-builder):

```javascript
new Table({
  width: { size: 9000, type: WidthType.DXA },
  rows: [new TableRow({
    children: [new TableCell({
      shading: { type: ShadingType.CLEAR, fill: "FCC4B0", color: "auto" },
      margins: { top: 240, bottom: 240, left: 360, right: 360 },
      children: [
        new Paragraph({
          children: [
            new TextRun({ text: "💡 ", font: "Segoe UI Emoji", size: 22 }),
            new TextRun({ text: "Volume & Methodology Note — ", bold: true }),
            new TextRun({
              text: "This report is based on " + volumeCount +
                    " raw datapoints mined from " + channels.join(", ") +
                    " between " + dateRange + ". Minimum 100-datapoint threshold met.",
            }),
          ],
        }),
      ],
    })],
  })],
})
```

The 💡 emoji needs `font: "Segoe UI Emoji"` to render properly in Word. Always
in its own TextRun.

---

## Quote board layout — 2-column grid

Top pains and top values are presented as 2-column quote grids — easier on the
eye than long lists, mirrors how a designer would lay out testimonials.

```javascript
new Table({
  width: { size: 9000, type: WidthType.DXA },
  columnWidths: [4500, 4500],
  rows: [
    new TableRow({
      children: [
        quoteCell(quote1.text, quote1.attribution),
        quoteCell(quote2.text, quote2.attribution),
      ],
    }),
    new TableRow({
      children: [
        quoteCell(quote3.text, quote3.attribution),
        quoteCell(quote4.text, quote4.attribution),
      ],
    }),
  ],
})

function quoteCell(quoteText, attribution) {
  return new TableCell({
    width: { size: 4500, type: WidthType.DXA },
    margins: { top: 180, bottom: 180, left: 240, right: 240 },
    borders: cellBorders("D0D0D0"),
    children: [
      new Paragraph({
        style: "QuoteBody",
        children: [new TextRun({
          text: '"' + quoteText + '"',
          italics: true,
        })],
      }),
      new Paragraph({
        style: "Attribution",
        children: [new TextRun({
          text: "— " + attribution,
          size: 18,
          color: "808080",
        })],
      }),
    ],
  });
}
```

Never use `WidthType.PERCENTAGE`. Always DXA (twentieths of a point).

---

## Cluster tables (Pains / Values / Objections)

Each cluster gets a header row showing label + metadata, followed by 3 quote
rows. Don't pack everything into a single mega-table — readability suffers.

Pattern per cluster:

```
H2: "1. [Pain label — one sentence]"

[Metadata paragraph in light grey:]
"Frequency: 18 of 124 quotes · Intensity: High · Triggered by: hiring new RevOps lead"

[3 verbatim quotes, each followed by attribution, indented 0.5"]

> "Manual data entry across 4 disconnected tools is killing my afternoons."
> — VP RevOps, mid-market SaaS · G2 · 2026-02-14

> "..."
> — ...

> "..."
> — ...
```

---

## Language Dictionary table

Two-column table with their words vs. mirrored language:

```javascript
new Table({
  width: { size: 9000, type: WidthType.DXA },
  columnWidths: [4500, 4500],
  rows: [
    headerRow(["What customers say", "What we should say back"]),
    languageRow('"Our reporting is a mess"', "Mirror it. Say: 'When reporting is a mess…' — don't paraphrase to 'when data quality is suboptimal.'"),
    languageRow('"Manually pulling numbers"', "Mirror it — don't say 'engaging in manual data extraction processes.'"),
    languageRow('"Streamlined" (rejected)', "Don't use this. Say 'cuts the time from 4h to 20min' — concrete."),
    // ... 10-20 rows
  ],
})
```

Header row uses dark background, white text:

```javascript
function headerRow(labels) {
  return new TableRow({
    tableHeader: true,
    children: labels.map(label => new TableCell({
      shading: { type: ShadingType.CLEAR, fill: "1A1A1A", color: "auto" },
      children: [new Paragraph({
        children: [new TextRun({ text: label, bold: true, color: "FFFFFF" })],
      })],
    })),
  });
}
```

---

## Sources Appendix

A simple full-width table — channel + URL + count + date range.

```javascript
new Table({
  width: { size: 9000, type: WidthType.DXA },
  columnWidths: [2000, 4000, 1500, 1500],
  rows: [
    headerRow(["Channel", "URL", "Quotes", "Date range"]),
    sourceRow("G2 (Competitor A)", "https://g2.com/products/comp-a/reviews", 42, "2025-01 to 2026-05"),
    sourceRow("Reddit r/RevenueOps", "https://reddit.com/r/RevenueOps/...", 38, "2024-12 to 2026-04"),
    // ... one row per source
  ],
})
```

---

## Bullet lists — numbering config

Never use unicode bullets (`•` `–` `*`) in body text. Always use docx-js
numbering for proper rendering and indent:

```javascript
const bulletNumbering = {
  config: [{
    reference: "bullets",
    levels: [{
      level: 0,
      format: LevelFormat.BULLET,
      text: "•",  // proper U+2022 bullet
      alignment: AlignmentType.LEFT,
      style: {
        paragraph: { indent: { left: 720, hanging: 360 } },
      },
    }],
  }],
};

// Use:
new Paragraph({
  numbering: { reference: "bullets", level: 0 },
  children: [new TextRun({ text: "Bullet content" })],
})
```

---

## Verbatim quote rule

The single most important rule for VoC docs: **preserve verbatim quotes**.

- Do not "clean up" grammar
- Do not fix typos in quotes
- Do not translate quotes (if mixing English and Swedish sources, keep original
  language and translate in a parenthetical if needed)
- Do not paraphrase to shorten — if a quote is long, use it long

This rule is enforced at the generation script level: the `quote_verbatim`
field from the tagged JSON is passed through unchanged to `TextRun.text`.

---

## Page-break hygiene

Cluster tables should not split across pages mid-row. Use:

```javascript
new Paragraph({
  pageBreakBefore: true,  // for major sections
  children: [...]
})
```

before each top-level section if the doc is > 8 pages. Otherwise let docx-js
flow naturally.

Visual QA in Step 8 will catch any awkward breaks — fix in the script and
regenerate.

---

## Output naming

```
/mnt/user-data/outputs/[company]_VoC_Report.docx
```

Use the company name from `icp-summary.md` if available, else from user input.
Replace spaces with underscores, strip non-ASCII characters from the filename
(content stays UTF-8).
