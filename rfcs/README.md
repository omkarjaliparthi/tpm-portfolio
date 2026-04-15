# RFCs (Technical Design Docs)

Technical decision docs for multi-team or high-risk architecture choices.

## Contents

- **[dual-payment-rails.md](./dual-payment-rails.md)** — RFC for adding PayPal alongside Stripe on Insights by Omkar. Covers context, four options considered, decision rationale, implementation plan, and retrospective lessons.
- **[home-grown-ephemeris-engine.md](./home-grown-ephemeris-engine.md)** — RFC for building the Insights Astrology API engine from primary sources (Meeus, VSOP87, ELP2000) vs wrapping Swiss Ephemeris or proxying a paid API. Four options scored, implementation in four phases, Meeus-validated test strategy.

---

*RFC format: Context → Options → Decision → Rationale → Implementation → Testing → Rollout. Compact variant of the Rust/Go RFC tradition. Lives alongside code in `/rfcs`.*
