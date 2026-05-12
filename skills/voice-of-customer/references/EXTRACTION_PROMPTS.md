# Extraction Prompts

Use these prompts during Step 5 to convert raw scraped text into tagged quotes.
Each prompt is designed to run on a chunk of 5–20 reviews / posts at a time.

---

## Master tagging prompt

Run this against any batch of raw quotes. It produces structured JSON output
that feeds the synthesis step.

```
You are processing raw voice-of-customer quotes. For each quote, extract and tag.

INPUT: [paste raw quotes — one per chunk, separated by ---]

OUTPUT FORMAT (JSON array, one object per quote):
{
  "quote_verbatim": "<exact text — do not paraphrase, do not clean grammar>",
  "source": "<channel — G2 / Capterra / Reddit / Trustpilot / YouTube / Other>",
  "source_url": "<URL if available>",
  "source_date": "<YYYY-MM-DD if available>",
  "reviewer_role": "<from review metadata, e.g. 'VP RevOps, mid-market SaaS'>",
  "company_size": "<from metadata, e.g. '50-200 employees'>",
  "star_rating": "<1-5 or null>",
  "category": "<Pain | Value | Objection | Trigger | Workaround>",
  "sub_category": "<from taxonomy in FRAMEWORKS.md>",
  "intensity": "<High | Medium | Low>",
  "segment_match": "<ICP segment if applicable, or 'unclear'>"
}

RULES:
1. Keep verbatim quote intact. Do not "clean" grammar, do not translate.
2. If a single review contains 2 distinct insights (e.g. one pain + one value),
   create 2 entries.
3. Intensity = High when language is strong ("nightmare", "broke us", "saved my
   job"). Medium = clear opinion without extremity. Low = soft mention.
4. Skip quotes shorter than 10 words unless intensity is High.
5. Skip marketing-speak ("game-changing", "world-class") — that's a tell of a
   solicited review, not a real one.
6. If category is unclear, mark as "Pain" by default and flag for review.
```

---

## Pain-specific deepening prompt

After tagging, run this on the pain-tagged quotes to surface JTBD layer:

```
For each pain quote, identify the Job-to-be-Done buried inside.

INPUT: [paste pain quotes]

For each, output:
{
  "quote": "<original>",
  "situation": "<when X happens>",
  "motivation": "<I want to Y>",
  "expected_outcome": "<so I can Z>",
  "symptom_or_job": "<'symptom' if the quote describes only the surface frustration; 'job' if it reveals the underlying outcome they're hiring a solution to deliver>"
}

If you cannot extract a clear JTBD job (only a vague complaint), mark as
"symptom". Pain clusters of mostly symptoms tell us we haven't dug deep
enough — we need to mine more.
```

---

## Value-specific prompt

Use on 4-5-star quotes that praise the competitor. We want to understand
*what's working* about alternatives so we can either match or differentiate.

```
For each value-tagged quote:
{
  "quote": "<verbatim>",
  "value_proposition": "<one-line label>",
  "competitor_mentioned": "<which product the reviewer is praising>",
  "alternative_they_left": "<what they used before, if mentioned>",
  "outcome_specificity": "<High | Med | Low — based on whether they cite a number or only an adjective>"
}

The most useful value quotes have:
- Specific numbers ("cut my reporting time from 4h to 20min")
- Named alternatives ("we left Salesforce because…")
- Time horizons ("after 6 months we …")

Flag these as "High specificity" — they're the candidates for landing-page copy.
```

---

## Objection-specific prompt

Run on 1-2-star reviews + abandoned-trial / churn quotes:

```
For each objection-tagged quote:
{
  "quote": "<verbatim>",
  "objection_category": "<Price | Switching cost | Trust | Capacity fit | Integration | Internal politics | Risk>",
  "resolution_shown_in_data": "<if the quote also mentions how they got past it — e.g. 'tried the free trial and it worked'>",
  "competitor_referenced": "<if they cite a competitor as the reason they didn't switch>"
}

Objection quotes are gold for sales enablement and FAQ pages. Tag aggressively.
```

---

## Trigger-specific prompt

Trigger quotes are rare but high-value. Look for quotes that mention what
*started* the buying journey:

```
For each quote that mentions an initiating event:
{
  "quote": "<verbatim>",
  "trigger_type": "<Hiring | Funding | Growth pain | Compliance | Competitive pressure | Tool failure | Seasonal>",
  "time_to_decision": "<if mentioned — '2 weeks', '3 months', etc.>",
  "decision_maker_signal": "<who in the org started looking, if mentioned>"
}

Triggers go into the cold-email-skill and ad-ideation-4-angles for hook
generation. Quote with a specific trigger event + 2-week window is the highest
value quote you can find.
```

---

## Language pattern extraction prompt

After clustering, run this against all High-intensity quotes to build the
language dictionary:

```
Read these 30-50 quotes. Extract:

1. NOUNS / PHRASES customers use to describe the pain:
   - "[exact phrase]"
   [list 8-15]

2. NOUNS / PHRASES customers use to describe the desired outcome:
   - "[exact phrase]"
   [list 8-15]

3. VERBS that signal active buying mode (urgent action):
   - "[verb + context]"
   [list 5-10]

4. INDUSTRY JARGON customers actively reject or mock:
   - "[jargon phrase] (rejected)"
   [list 3-8]

5. TONE SIGNATURE:
   - Formal / casual:
   - Technical / plain:
   - Emotional / clinical:
   - Verbose / terse:

Output as markdown. This becomes the seed for `voice-of-customer.skill.md`.
```

---

## Anti-paraphrasing reminder

When in doubt, **quote verbatim**. The customer's own words — typos, grammar
errors, unusual word choices — are the most valuable artifact this skill
produces. Marketing copy that mirrors customer language outperforms polished
copy 2-5x in conversion. Don't optimize the quotes for readability — they're
data, not prose.
