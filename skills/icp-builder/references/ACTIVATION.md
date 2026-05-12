# ICP Activation Playbook
*Source: How to Scale Your Business with Your Ideal Customer in Mind — HubSpot x Clearbit*

Load this file during Step 8 when building activation recommendations.

---

## What Activation Means

Building an ICP document is only half the job. Activation is putting it to work
across every channel and team so it actually changes what people do day-to-day.

> "Your ICP is your center of gravity for putting data to work." — Kevin Tate, CMO at Clearbit

An ICP isn't effective if it just lives on a slide. Every team — marketing, sales,
CS, product — needs updated playbooks that reflect the new ICP definition.

---

## Marketing Activation

### Paid Advertising
- Use ICP firmographic criteria to build targeted ad audiences — industry, company
  size, job title, technology used
- Use offline conversion tracking to feed CRM data back into ad platforms (Facebook,
  Google, LinkedIn) — this ensures you're only spending money on ICP-matching accounts
- Exclude Red-tier companies from targeting explicitly, not just by omission
- Match ad messaging to the ICP's emotional triggers and pain points, not product features

**Channel selection:** Choose channels where your ICP actually spends time. B2B buyers
are often on LinkedIn, industry Slack communities, and niche forums — not just Google.
Do the research rather than defaulting to the obvious channels.

### Content & Messaging
Map content to what the ICP actually cares about — problems they have, questions they
ask, outcomes they want:

| ICP Role | Problems | Questions | Wants |
|---|---|---|---|
| Ops/RevOps | Can't see what's in flight | How do I get teams aligned? | Visibility without meetings |
| Marketing Leader | Budget efficiency low | Where should I focus spend? | Know which accounts are in-market |
| CEO/CXO | CAC increasing, profitability dipping | How do I attract better leads? | Less time on bad-fit prospects |

Lead with pain points in your messaging, not features. The ICP's emotional layer
(what keeps them up at night) should drive your headlines and CTAs.

### Website Personalization
ICP-based web personalization delivers meaningfully different experiences to matched
vs. unmatched visitors:

**ICP visitor (firmographic match detected):**
- Show sales-oriented CTA ("Talk to us", "Book a demo")
- Messaging: enterprise/team-focused language
- Social proof: customer logos and case studies from their segment

**Non-ICP visitor:**
- Show self-serve or trial CTA
- Messaging: accessible, lower-friction
- Social proof: volume and breadth

Tools like Clearbit Reveal (or equivalent intent data platforms) can de-anonymize
website visitors and trigger personalized experiences via Optimizely, Mutiny, or
similar. Greenhouse increased enterprise demo conversion by 15% using this approach.

### Lead Scoring
Update lead scoring models to reflect ICP criteria. An ICP-matched MQL means
something specific and shared across teams:

> "There are endless hours of debate to be had over what the Q means in MQL and SQL.
> But with a shared concept of an ICP, you know the factors that define your ideal
> customer up and down the chain." — Kevin Tate, CMO at Clearbit

---

## Sales Activation

### Outbound Prioritization
- Build outreach lists exclusively from Green-tier companies — no exceptions for "big logos"
- Score inbound leads against ICP criteria before routing to reps
- Reps should be able to identify Green vs. Yellow vs. Red in the first 5 minutes of
  a discovery call using firmographic questions — before investing in a full demo

### Early Disqualification
Train reps on Red-tier signals to use as early disqualifiers:
- Company size outside the ICP range
- No internal champion in the right role
- Wrong industry or business model
- Requesting heavy customization upfront
- Deal initiated top-down without an operational owner

The goal is to have a richer pipeline of fewer, better-fit deals — not more volume.

### Intent-Based Prospecting
Intent signals help identify ICP accounts that are actively in-market:
- ICP companies visiting your website (especially pricing and case study pages)
- ICP accounts that haven't engaged in a while (re-engagement signal)
- New buying committee members joining ICP accounts (signal of organizational change)

Use these signals to trigger personalized outreach that references specific context —
not generic sequences.

### Compensation Alignment
If the ICP definition changes significantly (e.g., moving upmarket), sales compensation
and quotas may need to change too. Higher ACV deals have longer cycles — reps can't
be measured the same way on enterprise vs. SMB. Flag this for leadership if relevant.

---

## Customer Success Activation

### Onboarding Prioritization
Use ICP tier to inform CS resource allocation at onboarding:
- Green-tier customers: High-touch onboarding, dedicated CSM, proactive QBRs
- Yellow-tier: Structured onboarding with lighter-touch follow-up
- Red-tier (if they got through): Flag as churn risk, escalate early

### Expansion Playbook
ICP customers who grow are your best expansion targets:
- Track firmographic changes — if a 200-person company grows to 400, that's an
  expansion trigger
- Division hopping: identify other teams or departments that could benefit
- Reference and referral programs work best with Green-tier customers — build
  systematic asks into the renewal/expansion process

### Churn Prevention
Use ICP fit as an early churn predictor:
- Red-tier customers who slipped through are high churn risk — flag at onboarding
- Watch for missing implementation owners or champions who leave
- Track product adoption velocity in the first 30/60/90 days — ICP customers
  typically reach value faster

---

## Product & Engineering Activation

When the ICP changes, the product roadmap often needs to follow:
- Moving upmarket → likely needs more admin controls, SSO, compliance features,
  audit logs, enterprise security requirements
- Moving downmarket → needs higher-volume infrastructure, simpler UX, self-serve onboarding
- New vertical → may need industry-specific features, integrations, or compliance

If the user is considering ICP expansion, flag that product may need to be part of
the conversation early — not as an afterthought.

---

## ICP Evolution Triggers

ICPs aren't static. Revisit and potentially redefine when:
- **~$10M ARR with 2–3 years of renewal data:** Now you have real LTV cohort data.
  Do a full LTV ICP analysis and validate your assumptions.
- **Moving upmarket:** Frame.io rebuilt their ICP from scratch when they moved upmarket.
  The buyer profile, messaging, sales motion, and product requirements all changed.
- **Success criteria shift:** Early stage optimized for bookings/ARR. As you mature,
  optimize for LTV and retention. This often changes which customers are "ideal."
- **Market changes:** Competitive pressure, category shifts, or platform changes can
  make previously Red segments newly viable — and previously Green segments less attractive.

> "Your ICP is ever evolving. It's not something that's set in stone. As your product
> gets better, your go-to-market strategy changes, your company grows, and you get more
> resources, you can expand that definition." — Daniel Murray, Head of Media, Sales Impact Academy

---

## Activation Checklist (Use in Step 8)

Before closing out the ICP session, confirm the user has a plan for each:

- [ ] Marketing: Will targeting, lead scoring, and ad audiences be updated?
- [ ] Marketing: Does the website need ICP-based personalization?
- [ ] Sales: Do reps have Green/Yellow/Red criteria they can apply in discovery?
- [ ] Sales: Is outbound list-building process updated to Green-only?
- [ ] CS: Is onboarding triage updated to reflect ICP tier?
- [ ] CS: Is there an expansion playbook for growing Green-tier accounts?
- [ ] Product: Does this ICP change have product roadmap implications?
- [ ] Leadership: Is the validation schedule set and owned?
