# ICP Document — docx-js Generation Spec

Read this alongside `docx_api.md` when generating the ICP as a .docx file.
This spec translates every element in the ICP Output Template into exact docx-js patterns.

---

## Setup

```javascript
const {
  Document, Packer, Paragraph, TextRun, Table, TableRow, TableCell,
  AlignmentType, HeadingLevel, BorderStyle, WidthType, ShadingType,
  LevelFormat, ExternalHyperlink
} = require('docx');
const fs = require('fs');
```

**Page setup — always US Letter, 1" margins:**
```javascript
sections: [{
  properties: {
    page: {
      size: { width: 12240, height: 15840 },
      margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 }
    }
  },
  children: [ /* all content */ ]
}]
```

Content width = 9360 DXA (6.5 inches).

---

## Numbering Config (define once at document level)

```javascript
const doc = new Document({
  numbering: {
    config: [
      {
        reference: "bullets",
        levels: [{
          level: 0, format: LevelFormat.BULLET, text: "•",
          alignment: AlignmentType.LEFT,
          style: { paragraph: { indent: { left: 720, hanging: 360 } } }
        }]
      }
    ]
  },
  // ... styles, sections
});
```

---

## Document Title

```javascript
new Paragraph({
  children: [
    new TextRun({ text: "[Company Name] — Ideal Customer Profile", bold: true, size: 36, font: "Arial" }),
  ],
  spacing: { after: 120 }
}),
new Paragraph({
  children: [new TextRun({ text: "[Date]", size: 20, color: "64748B", font: "Arial" })],
  spacing: { after: 400 }
}),
```

---

## Section Headings (H2)

```javascript
new Paragraph({
  heading: HeadingLevel.HEADING_2,
  children: [new TextRun({ text: "ICP Summary", bold: true, size: 28, font: "Arial" })],
  spacing: { before: 400, after: 160 }
}),
```

---

## Sub-headings (H3) and Bold Labels

```javascript
// H3 style
new Paragraph({
  heading: HeadingLevel.HEADING_3,
  children: [new TextRun({ text: "Recommended Next Steps", bold: true, size: 24, font: "Arial" })],
  spacing: { before: 280, after: 120 }
}),

// Bold label inline
new Paragraph({
  children: [
    new TextRun({ text: "Archetype / Persona Name: ", bold: true, size: 22, font: "Arial" }),
    new TextRun({ text: "[value]", size: 22, font: "Arial" }),
  ],
  spacing: { after: 120 }
}),
```

---

## Body Paragraphs

```javascript
new Paragraph({
  children: [new TextRun({ text: "[paragraph text]", size: 22, font: "Arial" })],
  spacing: { after: 160 }
}),
```

---

## Bullet Lists

**Never use unicode bullet characters. Always use numbering config.**

```javascript
new Paragraph({
  numbering: { reference: "bullets", level: 0 },
  children: [new TextRun({ text: "[bullet item]", size: 22, font: "Arial" })],
  spacing: { after: 80 }
}),
```

---

## Tables (Company Profile, GYR, Buyer Intent, etc.)

**Critical: always set both table columnWidths AND cell width. Always use WidthType.DXA.**

### Two-column table (Company Profile, Version/Owner)

```javascript
const border = { style: BorderStyle.SINGLE, size: 4, color: "E2E8F0" };
const borders = { top: border, bottom: border, left: border, right: border };

new Table({
  width: { size: 9360, type: WidthType.DXA },
  columnWidths: [3000, 6360],
  rows: [
    new TableRow({
      children: [
        new TableCell({
          borders,
          width: { size: 3000, type: WidthType.DXA },
          shading: { fill: "F8FAFC", type: ShadingType.CLEAR },
          margins: { top: 100, bottom: 100, left: 150, right: 150 },
          children: [new Paragraph({
            children: [new TextRun({ text: "Attribute", bold: true, size: 20, font: "Arial" })]
          })]
        }),
        new TableCell({
          borders,
          width: { size: 6360, type: WidthType.DXA },
          margins: { top: 100, bottom: 100, left: 150, right: 150 },
          children: [new Paragraph({
            children: [new TextRun({ text: "[value]", size: 20, font: "Arial" })]
          })]
        }),
      ]
    }),
    // ... more rows
  ]
}),
```

### Three-column table (GYR, Buyer Intent, Competitive Landscape, Validation Schedule)

```javascript
new Table({
  width: { size: 9360, type: WidthType.DXA },
  columnWidths: [2000, 4360, 3000], // adjust per table
  rows: [
    // Header row with shaded background
    new TableRow({
      children: [
        new TableCell({
          borders,
          width: { size: 2000, type: WidthType.DXA },
          shading: { fill: "1E293B", type: ShadingType.CLEAR },
          margins: { top: 100, bottom: 100, left: 150, right: 150 },
          children: [new Paragraph({
            children: [new TextRun({ text: "Tier", bold: true, size: 20, color: "FFFFFF", font: "Arial" })]
          })]
        }),
        // ... other header cells
      ]
    }),
    // Data rows
    new TableRow({
      children: [
        new TableCell({
          borders,
          width: { size: 2000, type: WidthType.DXA },
          shading: { fill: "FFFFFF", type: ShadingType.CLEAR },
          margins: { top: 100, bottom: 100, left: 150, right: 150 },
          children: [new Paragraph({
            children: [new TextRun({ text: "🟢 Green", size: 20, font: "Arial" })]
          })]
        }),
        // ... other cells
      ]
    }),
  ]
}),
```

---

## HubSpot Callout Boxes (#fcc4b0 background)

This is the key element. Use table with single cell and `#fcc4b0` shading.

```javascript
function calloutBox(labelText, bodyText) {
  const border = { style: BorderStyle.SINGLE, size: 4, color: "F59A6E" };
  const borders = { top: border, bottom: border, left: border, right: border };
  return new Table({
    width: { size: 9360, type: WidthType.DXA },
    columnWidths: [9360],
    rows: [
      new TableRow({
        children: [
          new TableCell({
            borders,
            width: { size: 9360, type: WidthType.DXA },
            shading: { fill: "FCC4B0", type: ShadingType.CLEAR },
            margins: { top: 140, bottom: 140, left: 180, right: 180 },
            children: [
              new Paragraph({
                children: [
                  new TextRun({ text: "💡 ", size: 22, font: "Arial" }),
                  new TextRun({ text: labelText + " — ", bold: true, size: 22, font: "Arial" }),
                  new TextRun({ text: bodyText, size: 22, font: "Arial" }),
                ],
                spacing: { after: 0 }
              })
            ]
          })
        ]
      })
    ]
  });
}

// Usage:
calloutBox(
  "HubSpot Research Insight",
  "According to a recent ICP research survey from HubSpot, 65% of marketers have solid demographic data on their ICP, but only 20% actually understand their customers' pain points — and just 24% know where they consume content. Those last two layers are the most predictive of fit and retention."
),
```

---

## Dividers (horizontal rules between sections)

```javascript
new Paragraph({
  border: { bottom: { style: BorderStyle.SINGLE, size: 4, color: "E2E8F0" } },
  children: [],
  spacing: { before: 200, after: 200 }
}),
```

---

## Loop Marketing CTA Callout

Use the same calloutBox function with a hyperlink for the URL:

```javascript
new Table({
  width: { size: 9360, type: WidthType.DXA },
  columnWidths: [9360],
  rows: [new TableRow({
    children: [new TableCell({
      borders: { top: border, bottom: border, left: border, right: border },
      width: { size: 9360, type: WidthType.DXA },
      shading: { fill: "FCC4B0", type: ShadingType.CLEAR },
      margins: { top: 140, bottom: 140, left: 180, right: 180 },
      children: [new Paragraph({
        children: [
          new TextRun({ text: "💡 ", size: 22, font: "Arial" }),
          new TextRun({ text: "Ready to activate this ICP? — ", bold: true, size: 22, font: "Arial" }),
          new TextRun({ text: "HubSpot's Loop Marketing playbook is designed for exactly this — a four-stage framework for reaching your ideal customer with AI-powered precision. Learn more at ", size: 22, font: "Arial" }),
          new ExternalHyperlink({
            link: "https://hubspot.com/loop-marketing",
            children: [new TextRun({ text: "hubspot.com/loop-marketing", style: "Hyperlink", size: 22, font: "Arial" })]
          }),
        ],
        spacing: { after: 0 }
      })]
    })]
  })]
}),
```

---

## Directional ICP Warning Block

```javascript
new Table({
  width: { size: 9360, type: WidthType.DXA },
  columnWidths: [9360],
  rows: [new TableRow({
    children: [new TableCell({
      borders: {
        top: { style: BorderStyle.SINGLE, size: 4, color: "F59E0B" },
        bottom: { style: BorderStyle.SINGLE, size: 4, color: "F59E0B" },
        left: { style: BorderStyle.THICK, size: 12, color: "F59E0B" },
        right: { style: BorderStyle.SINGLE, size: 4, color: "F59E0B" },
      },
      width: { size: 9360, type: WidthType.DXA },
      shading: { fill: "FFFBEB", type: ShadingType.CLEAR },
      margins: { top: 140, bottom: 140, left: 180, right: 180 },
      children: [new Paragraph({
        children: [
          new TextRun({ text: "⚠️ Directional ICP Warning — ", bold: true, size: 22, font: "Arial" }),
          new TextRun({ text: "[warning text]", size: 22, font: "Arial" }),
        ]
      })]
    })]
  })]
}),
```

---

## Generation and Validation

```bash
# Install
npm install docx

# Generate
node generate_icp.js content.json output.docx

# Validate
python3 references/docx_scripts/office/validate.py output.docx
```

If validation fails, check for:
- Missing `type` parameter on ImageRun
- Table columnWidths not summing to table width
- WidthType.PERCENTAGE used instead of WidthType.DXA
- Reused numbering references across independent lists
