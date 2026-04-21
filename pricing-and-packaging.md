# Pricing & packaging · reasoning framework

> **What this is.** The way I think about pricing developer APIs and consumer SaaS. Framework, not prices. The actual tier numbers live on the product pages; they change.

---

## The packaging precedes the pricing

The most common pricing mistake is deciding the numbers before deciding the *shape*. Three shape questions must answer first:

1. **Who is the buyer?** Developer, operator, end-user. Each has different price sensitivity, different comparison frames, different expansion paths.
2. **What is the unit?** Seats, requests, projects, tokens, outcomes. The wrong unit creates misaligned incentives — buyer optimizes against the meter, not against value.
3. **What is the expansion vector?** The buyer's successful state either pulls them to a higher tier (good) or lets them stay in the current tier indefinitely (bad for revenue, sometimes good for growth).

Only after those three are answered does the numeric conversation make sense.

---

## Axes I reason along

### 1 · Tier count

- **Zero tiers · single price** — easiest to explain, hardest to capture willingness-to-pay. Right for single-purpose products with a narrow buyer.
- **Two tiers · free + paid** — the "indie SaaS" shape. Good when there's a clear line between "learning" and "using."
- **Three tiers · free + paid + enterprise** — classic developer-tool shape. Three personas, three prices. Common for a reason; easy to get wrong by miscalibrating the middle.
- **Four+ tiers** — rarely justified. Usually means the team couldn't decide. Buyers experience it as noise.

### 2 · Unit of measurement

- **Seats** — good for collaboration products, bad for anything the user uses alone
- **Usage (requests, tokens, compute)** — fair by construction, anxious for buyers who can't bound cost
- **Outcomes (completed runs, generated artifacts)** — aligns incentives best, hardest to define cleanly
- **Projects / workspaces** — a compromise unit that often stalls out as an expansion trigger
- **Flat monthly** — anchors cost expectations, leaves usage data on the floor unless augmented with soft quotas

### 3 · Overage behavior

- **Hard cap** — buyer predictability is maximal. Churn risk at the ceiling is maximal too.
- **Soft overages** — customer keeps using; a line item appears next bill. Requires reliable usage tracking and clear communication.
- **Automatic upgrade at quota** — aggressive; only works when the tier above is an obvious natural next step
- **Block + email** — the buyer decides after they hit the ceiling; high-trust posture

Choice depends more on buyer risk tolerance than on math.

### 4 · Free tier posture

- **Try-before-buy** — sufficient to evaluate, insufficient to build anything real. Fine if conversion is the only goal; terrible for developer trust.
- **Genuinely useful free** — real product for hobby/low-volume use. Drives loyalty. Eats a slice of revenue.
- **Time-limited trial, no free tier** — works for enterprise; fails for developer APIs where comparison shopping is common.

### 5 · Price anchoring

- **Reference to competitors.** Buyers compare; anchor to the cheapest and you're in a price war. Anchor to the most expensive and you either own a premium slot or lose entirely.
- **Reference to value delivered.** Hardest to communicate; most durable when it works. Requires a measurable value story the buyer already believes.
- **Reference to cost.** Cost-plus pricing is a trap for software — cost is nearly fixed, value is not.

### 6 · Naming

- Tier names that describe the *buyer* self-sort faster than names that describe the *features*
- `Free / Pro / Enterprise` works because the buyer knows which one they are
- `Bronze / Silver / Gold` introduces a ranking without naming a buyer — often underperforms
- `Starter / Growth / Scale` works for growth-stage products, signals trajectory

---

## Rules I hold

1. **List your prices.** Hidden prices cost you developer trust. The only exception is the enterprise tier where true negotiation is the product.
2. **Don't punish the buyer in the paid tier.** Removing features in the paid tier that existed in the free tier is a betrayal shape. Quotas scale; capabilities do not regress.
3. **The middle tier must make obvious sense.** If the only buyers are enterprise (top tier) and bootstrappers (free tier), the middle is decoration.
4. **Frequency of repricing matters less than clarity of the prior price.** Raise prices on new signups, grandfather existing customers; communicate in advance.
5. **Annual pricing should save the buyer real money.** 17–20% off is standard; below 10% is meaningless, above 25% signals the monthly is inflated.
6. **Enterprise calls are not for small prospects.** "Contact sales" for a $200/mo deal burns both sides' time.

---

## Common failure modes

- **Pricing before product-market fit.** You'll end up defending the price and not the product.
- **Copying a competitor's tier shape with your own numbers.** Their shape reflects their buyer; your buyer may be different.
- **Charging for the axis your users don't value.** If the buyer doesn't care about speed, charging for speed won't convert.
- **Treating pricing as a one-time decision.** Pricing is a feature with a changelog, not a launch artifact.
- **Using "unlimited" loosely.** Unlimited with an acceptable use policy is fine; unlimited with a quota hidden in the docs is deception.

---

## A worked example of how I'd approach a new product

Not real, not a specific product — a thought exercise for readers.

**Scenario** · A new developer-facing API, similar shape to the astrology API.

1. **Buyer** · developer building a consumer app; secondary buyer is an agency integrating for a client
2. **Unit** · requests per month at the meter, but priced as flat tiers with soft quotas — developers hate anxiety-pricing
3. **Tier count** · three (free / paid / enterprise) — the shape most devs compare against
4. **Free** · enough volume to build a real hobby project; ~1/20 of the paid tier quota
5. **Paid** · priced against the "I'd pay a Netflix subscription for this" frame, somewhere in the $30–60/mo band; specific number set by value benchmarking, not cost
6. **Enterprise** · no listed price; true enterprise features (SSO, SLA, dedicated support, custom terms)
7. **Overage** · email at 80%, block at 100%, upgrade path obvious. No surprise charges.
8. **Names** · buyer-persona names. No metal colors.

The *numbers* come last in this flow, and they get revisited as usage data arrives.

---

*Related reading · [ADR 0005 · Pricing tier psychology](./adrs/0005-pricing-tier-psychology.md) · [PRD · Astrology API launch](./prds/astrology-api-launch.md)*
