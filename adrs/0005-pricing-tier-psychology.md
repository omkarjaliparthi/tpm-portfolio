# ADR 0005 · Pricing tier psychology

**Status** · Accepted · 2026-03-22
**Author** · Omkar Jaliparthi
**Program** · Insights Astrology API

---

## Context

Developer-facing APIs have a well-trodden pricing grammar: free tier for learning, paid tier for production, enterprise tier for scale. Within that grammar, tier *count*, *price gaps*, and *naming* shape self-selection more than feature lists do. This ADR records the reasoning, not the numbers.

## Options

### A · Single paid tier + free

- Simplest; no pricing-page cognitive load
- Leaves enterprise budget on the table; no expansion lane
- Low price discrimination across use cases

### B · Three tiers · free · paid · enterprise

- Industry-standard for developer APIs
- Each tier has a distinct buyer persona: *learner*, *builder*, *business*
- Classic trap: middle tier under-priced relative to the value the buyer gets

### C · Usage-based only

- Fair by construction · pay for what you use
- Creates anxiety at purchase time — buyers can't bound cost
- Harder to forecast revenue and gross margin

### D · Three tiers with metered overages

- Fixed monthly price anchors cost expectations
- Overages handle spikes without forcing an upgrade
- Most complex to implement

## Decision

**Option B — three tiers, flat monthly pricing, no overages at launch.** Option D is the likely v2 once usage data justifies the complexity.

## Principles

1. **Tier names describe the buyer, not the feature.** *Dev* · *Studio* · *Scale* signals a self-selection path without reading the feature list.
2. **Gap between tiers must feel proportional.** If the paid tier is 10× the free quota, the enterprise tier should be 10× the paid quota — not 100×.
3. **Free tier must be genuinely useful.** A free tier that can't build a real thing is a conversion trap, not a funnel.
4. **No feature withheld in a way the buyer will read as punitive.** Rate limits and quota scale, but core computation accuracy does not differ across tiers.
5. **Price listed, not "contact sales," until revenue justifies it.** Gated pricing signals either enterprise-only or early stage. Neither helps developer trust.

## Consequences

**Accepted:**
- Usage-based pricing deferred; some high-volume customers are either over-paying or over-quota
- Metered overages delayed; an occasional customer churns at the quota edge instead of paying more
- Three-tier page requires clearer feature matrix than a one-paid-tier page would

**Won:**
- Buyers self-select in under thirty seconds on the pricing page
- Revenue is forecastable from subscriptions, not usage distribution
- Tier boundaries give product a natural axis to rank feature requests (which tier funds this?)

## Exit criteria

Revisit if any hold:

- ≥15% of paid customers churn at the quota ceiling
- Free tier conversion rate drops below target for two consecutive quarters
- Enterprise deals require custom terms that the page can't express

## Related

- [Pricing & packaging reasoning](../pricing-and-packaging.md)
- [PRD · Astrology API · Public Launch](../prds/astrology-api-launch.md)
