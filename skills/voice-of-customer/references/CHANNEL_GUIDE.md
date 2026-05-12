# Channel Selection Guide

Load this during Step 4 (channel mix). Match the ICP type to the priority
channel list. Don't waste time mining a platform where your ICP doesn't
actually live.

---

## B2B SaaS — mid-market and enterprise

**Priority 1 — must mine:**
- G2 (verified buyer reviews, segmentable by company size)
- Capterra (smaller companies, complementary to G2)
- TrustRadius (enterprise, more in-depth reviews)

**Priority 2 — usually high signal:**
- Reddit subs: r/sales, r/SaaS, r/marketing, r/RevenueOperations,
  r/[specific category — analytics, devtools, CX, etc.]
- LinkedIn comments on competitor posts (when discoverable)
- Gartner Peer Insights (enterprise category)

**Priority 3 — only if user has access:**
- Internal sales-call transcripts (Gong, Chorus, Fireflies)
- Support tickets (Zendesk, Intercom)
- NPS / CES open comments

**Skip unless ICP explicitly hangs there:**
- Amazon (rarely a B2B SaaS channel)
- Trustpilot (mixed-quality, low signal for B2B)

---

## B2B SaaS — SMB / freemium / PLG

**Priority 1:**
- Product Hunt comments (especially launch threads)
- Reddit subs: r/Entrepreneur, r/smallbusiness, r/[category]
- App store reviews if there's a mobile/desktop app

**Priority 2:**
- Twitter / X (smaller companies more vocal here)
- YouTube tutorial comments on the category
- Indie Hackers forum

**Priority 3:**
- G2 / Capterra (volume is lower in this segment)

---

## B2C / CPG / DTC

**Priority 1:**
- Amazon reviews (especially 1–3 star)
- Trustpilot (brand-level reviews)
- Reddit subs: r/[category — skincare, fitness, etc.]
- Instagram comments on competitor posts

**Priority 2:**
- TikTok comments on category content
- Sephora / Ulta / Target reviews (if applicable)
- Reviews.io
- Beauty / lifestyle forums (Reddit-equivalents in your category)

**Priority 3:**
- YouTube comments on review / unboxing videos

**Skip unless category-fit:**
- G2 / Capterra (B2B SaaS)
- Gartner (B2B enterprise)

---

## B2B Services (consulting, agency, dev shop)

**Priority 1:**
- Clutch.co reviews
- LinkedIn recommendations on competitor partners' profiles
- Reddit subs: r/Entrepreneur, r/SmallBusiness, category subs

**Priority 2:**
- G2 (for tech-adjacent services)
- Twitter / X discussions about agency selection
- Industry-specific forums (e.g., r/agency, r/freelance)

**Priority 3:**
- Email cold-outreach response patterns (if user has them)

**Note:** B2B services often need *referral-pattern mining* in addition to
review mining — who refers them, what they say in the referral. Ask the user
if they have testimonial videos / case studies and treat those as a channel.

---

## Local services / brick-and-mortar

**Priority 1:**
- Google Maps reviews (massive volume, mixed quality)
- Yelp (varies by region — strong in US, weak in EU)
- Facebook reviews (community-driven, more emotional)

**Priority 2:**
- Tripadvisor (for hospitality / travel)
- Industry-specific platforms (e.g., Healthgrades for medical)

**Priority 3:**
- Local subreddit r/[city] mentions

---

## Education / EdTech / coaching

**Priority 1:**
- Course platform reviews (Coursera, Udemy comment sections)
- Reddit subs: r/learnprogramming, r/[topic], r/cscareerquestions
- Trustpilot for paid bootcamps / coaching

**Priority 2:**
- YouTube tutorial comments
- Twitter testimonial threads
- Instagram DM-style testimonials (harder to scrape)

---

## How to pick when ICP is ambiguous

Ask the user one question: **"Where does your last cohort of customers spend
time online when they're researching solutions like yours?"** Their answer
points to the priority channel.

If they don't know — that's itself a finding. Flag it: "Not knowing where your
buyers research is a research project of its own. We'll do a multi-channel
scrape and use volume as the answer."

---

## Volume estimates per channel

Rough rule of thumb per competitor:

| Channel | Quotes per competitor per scrape |
|---|---|
| G2 | 50–200 |
| Capterra | 30–100 |
| Trustpilot | 50–300 (very varied) |
| Amazon | 100–5000 (per product) |
| Reddit (single subreddit) | 20–100 (in 3–6 months) |
| YouTube comments | 50–500 per video |
| LinkedIn | Low — typically 5–30 |
| Clutch | 20–80 |

Multiply by number of competitors and channels chosen to estimate scrape time
and processing time.

---

## Anti-patterns

- **Don't mine your own reviews exclusively.** You'll confirm what you already
  know. Competitor reviews surface what your ICP wants but isn't getting.
- **Don't mine paid-shill platforms.** Some platforms allow vendors to solicit
  reviews. Discount accordingly — Trustpilot is notorious for this.
- **Don't ignore platform skew.** G2 skews positive (most reviews are 4–5
  star), Reddit skews critical (people post when they're frustrated).
  Triangulate.
- **Don't mine internal channels alone.** Your support tickets show pains
  *current* customers have, not pains that prevented prospects from buying.
  Need external channels for objection research.
