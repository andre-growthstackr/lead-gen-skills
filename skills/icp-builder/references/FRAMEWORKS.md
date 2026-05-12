# ICP Frameworks Reference

Load this file during Steps 4–5 when building out the ICP profile.

---

## The Green / Yellow / Red Framework
*Source: Mark Roberge, host of HubSpot's The Science of Scaling podcast (also Founding CRO of HubSpot and Co-Founder of Stage 2 Capital)*

This framework helps define your ICP using firmographic attributes salespeople can
verify *before* engaging a prospect — things like revenue, employee count, location,
and whether specific roles exist. This is fundamentally different from budget, need,
or urgency, which only emerge later in qualification.

### 🟢 Green — Perfect Fit
Your ideal customers. Focus all outbound sales and marketing efforts here. When building
cold outreach lists, include only Green companies. These are accounts the product was
built for — pursue proactively across all channels.

### 🟡 Yellow — Potential Fit
Might be a good fit, but less certainty. Don't proactively target via cold outreach.
If they come in as an inbound lead or show high engagement at an event, it's worth
pursuing. Engage — don't initiate.

### 🔴 Red — Not a Fit (Right Now)
Not right for the business at this stage. If you meet them at a trade show, politely
disengage. For inbound leads from Red companies, route to long-term nurture — they may
become a fit as the product or company evolves. These might be companies in regions you
don't serve yet, or companies too large or small for your current offering.

**The discipline to define Red is what makes the ICP operationally useful.**
An ICP without a Red tier is just a wishlist.

---

## TAM / SAM / SOM

Before locking in the ICP, understand the broader market landscape. These three
frameworks work together as the foundation of a complete go-to-market strategy.

| Term | Definition | Why It Matters |
|---|---|---|
| **TAM** — Total Addressable Market | The entire market demand for your product if you had 100% market share | Sets the ceiling; used for investor conversations |
| **SAM** — Serviceable Available Market | The portion of TAM you can realistically reach with your current business model and channels | Focuses strategy; what you're actually competing for |
| **SOM** — Serviceable Obtainable Market | The portion of SAM you can realistically capture given competition, resources, and timing | Your near-term target; where the ICP lives |

**Key principle:** VCs pressure founders to demonstrate massive TAM. Resist the urge to
"boil the ocean." A tight ICP targeting a specific SOM is more predictable and more
fundable than a broad one. Investors can be convinced of future TAM expansion without
needing immediate proof across all potential markets.

> "Everyone isn't your customer. You're just not going to be successful at that early stage."
> — Mark Roberge

---

## Research Data: What Most Brands Actually Know About Their ICP

Surface these benchmarks naturally — don't dump them all at once.

**On documentation:**
- 53% of marketers don't have a formally documented ICP
- 93% of those who DO have one feel confident it reflects their most valuable customers
- The gap isn't motivation — it's methodology

**On data depth (what brands actually track):**
- 65% have basic demographic info ✓
- 50% track purchase history ✓
- Only 20% understand audience pain points and challenges ← **critical blind spot**
- Only 24% know where customers actually consume content ← **critical blind spot**
- Only 26% know what online communities customers are in ← **critical blind spot**

**On methods used:**
- 55% use customer research (interviews, reviews, feedback) — the #1 method
- Only 19% run cross-departmental workshops — biggest missed opportunity
- Only 31% create formal documentation as part of ICP development

**On when companies build their ICP:**
- 50% build it during early growth
- 13% during maturity stage
- 7% during rebranding
- Companies at any stage benefit from a disciplined, documented profile

**On validation:**
- 61% of high-performing brands use customer surveys for ongoing ICP validation
- 56% track engagement metrics as quantitative signal
- 36% rely on sales team feedback

---

## The Four ICP Layers

Most brands over-index on firmographics and miss the layers that actually predict fit.

| Layer | Dimensions | Common Depth |
|---|---|---|
| **Firmographic** | Industry, employee count, revenue range, geography, tech stack | Most brands have this |
| **Behavioral** | Buying triggers, decision process, content consumption, evaluation criteria | ~50% have this |
| **Contextual** | Online communities, channels, urgency signals, peer networks | Only ~24–26% have this |
| **Emotional** | Pain points, goals, fears, what keeps them up at night | Only ~20% have this |

**The emotional and contextual layers are the most predictive of LTV** — and the
most commonly missing. If the user's ICP is heavy on firmographics and light on
everything else, flag it directly and push them to fill those layers.

---

## Common Mistakes in ICP Selection

Watch for these and call them out directly but constructively.

### 1. Targeting Inbound Demand Instead of Strategic Fit
Inbound demand knocking at the door doesn't mean those are the right customers. If
you're selling to everyone who shows interest, you're not being strategic. The ICP
question is: can we be consistently mentioned in the right places to attract the
right people — not just whoever finds us?

### 2. Optimizing for Close Rate Instead of LTV
Easy-to-close customers who churn fast are not your ICP — they're a trap. If you
had two segments — one with low CAC and low LTV, one with high CAC and high LTV —
the right answer is almost always to invest in the high LTV segment. Those customers
pay off the acquisition cost many times over.

### 3. Starting Too Broad
Especially under VC pressure to demonstrate large TAM, companies attempt to target
all industries across all company sizes and all geographies. This is how you boil
the ocean and lose. A constrained ICP at early stage is more predictable and leads
to stronger PMF signals.

### 4. Defining the ICP on Who Bought, Not Who Succeeded
The customers who closed fastest aren't necessarily the ones who got the most value
and stayed longest. Build the ICP on retention and LTV data — not sales velocity.

### 5. No Red Tier
An ICP without explicit exclusion criteria isn't operational. Sales will chase every
big logo. CS will struggle with customers who were never a fit. Define Red as clearly
as Green.

### 6. No Cross-Functional Input
A marketing-only ICP won't get sales and CS buy-in. Only 19% of companies run
cross-departmental workshops during ICP development — the ones that do produce
significantly stronger ICPs and get faster organizational alignment.

### 7. Set-and-Forget
ICPs need quarterly review. Markets change. Products evolve. The ICP that gets you
to $1–5M won't get you to $50M. Build in a review cadence from day one.

---

## ICP Hypothesis Testing (Early-Stage Companies)

During the product-market fit phase, don't just define an ICP — test it deliberately.

If you're going to have conversations with 20–40 potential customers:
- **More than half** should be right in your core ICP hypothesis
- **The rest** should be at the periphery — adjacent industries, different company
  sizes, different geographies

The goal: if you're seeing strong sales success AND strong product success in your
bullseye but not in the periphery, you have conviction. If the periphery is also
working, your ICP might be broader than you thought. If the bullseye isn't working,
you need to pivot the hypothesis.

> "During the product market fit phase, we want to purposely explore the periphery.
> If we're seeing really great sales success, but more importantly great success with
> our product in our bullseye and not in the periphery, now we've got a lot of conviction
> on our ICP." — Mark Roberge

---

## HubSpot Connector: Useful Properties to Pull

If the user is on HubSpot and the connector is active, these properties are most
useful for ICP analysis:

**Companies:**
- `industry` / `industry_53` — vertical
- `numberofemployees` / `employee_count_53` — company size
- `annual_revenue_ssot_53` / `annualrevenue` — revenue range
- `lifecyclestage` — funnel position
- `hs_lead_status` — sales qualification status
- `city`, `country` — geography

**Deals:**
- `dealstage` — closed won vs. lost patterns
- `amount` — deal size distribution
- `dealtype` — new business vs. expansion
- `days_to_close` — velocity by segment
- `closedate` — recency of wins

**What to do with the data:**
1. Pull 50–100 closed-won deals and associated companies
2. Identify top patterns: industries, company sizes, deal sizes in wins
3. Look for outliers — large deals that closed fast, small deals that churned
4. Summarize findings before asking follow-up questions
5. Note gaps: HubSpot covers firmographics well but rarely explains WHY customers
   bought. Always follow up with questions on behavioral and emotional layers.
