# PRD · Kriya — Insights Astrology API · Public v1 Launch

**Status** · Shipped v1.0 · 2026-04-15 · *now at v9.x · 109+ endpoints (last verified 2026-04-27)*
**Owner** · Omkar Jaliparthi · Founder / Product / Architecture
**Product** · Kriya — Insights Astrology API (formerly *Tuffys*) · [kriya.insightsbyomkar.com](https://kriya.insightsbyomkar.com)
**Parent business** · Omkar's Holistic Services LLC (DBA Insights by Omkar)

> **This is a historical PRD** — the v1-launch document. It captures the scope and reasoning at the moment of public launch. The product has since grown across nine semver versions; this PRD's success metrics, endpoint list, and risk register reflect the v1 state, not the current state. See the [case-study Outcomes & Lessons doc](https://github.com/omkarjaliparthi/insights-astrology-api-case-study/blob/main/docs/08-outcomes-and-lessons.md#where-it-is-now) for the current snapshot.

---

## 1. Problem

Astrology-adjacent products (meditation apps, dating apps, wellness platforms, horoscope bots) either:

- **Pay per-request to legacy APIs** (AstrologyAPI, Prokerala, FreeAstrologyAPI) — aging stacks, inconsistent response shapes, rate-limit cliffs, and per-tradition pricing that forces bundling.
- **Wrap the open-source Swiss Ephemeris** — GPL-licensed, hard to deploy serverlessly, requires shipping 100+ MB of ephemeris files, and carries a copyleft obligation that blocks most commercial apps.
- **Build in-house** — months of specialized work most teams don't have, even if the math is published.

Meanwhile the addressable market is real and sticky: spiritual/wellness apps ($1B+ segment), dating apps using compatibility as a retention lever, AI character products wanting ground-truth astrological data instead of hallucinated strings.

**No credible, modern, commercially-licensed API covers Western + Vedic + Hellenistic + electional traditions in one surface with first-class SDKs.** That's the gap.

## 2. Users & segments

| Segment | What they need | How they buy |
|---|---|---|
| **Indie builders / solo devs** | Natal chart + daily transits, TS or Python SDK, free to explore | Free tier, upgrade on scale |
| **Wellness / spiritual apps (Series A/B)** | Reliable uptime, predictable pricing, all tradition coverage | Studio tier, monthly |
| **AI product teams** | Structured JSON to ground LLM outputs, low latency | Studio or Scale, annual |
| **Dating / compatibility apps** | Synastry + composite charts at 10K+/day | Scale tier, custom |
| **Academic / research** | Batch historical positions, full accuracy disclosure | Studio tier + data license |

## 3. Goals

- **P0** — launchable public v1 with all 43 endpoints covering Western + Vedic + Hellenistic + electional traditions
- **P0** — stateless auth + rate limiting that scales to thousands of keys with zero database dependency
- **P0** — official SDKs in TypeScript, Python, and Go, each <200 lines, zero runtime deps, MIT-licensed
- **P1** — interactive API docs + pricing page + live demo that converts visitors in one session
- **P1** — accuracy statements with sources (Meeus, IMCCE VSOP87, ELP2000, Standish) — defensible against any audit
- **P2** — developer portal for key management (deferred — JWT minting via CLI is acceptable for v1)

## 4. Non-goals

- **No GUI chart renderer as a product.** A reference wheel ships in the demo; rendering is not the business.
- **No interpretive text generation.** The API returns data, not narratives. Customers layer their own LLMs or content.
- **No AI astrologer chatbot.** That's a separate product line (belongs to Insights by Omkar consumer SaaS).
- **No Swiss Ephemeris dependency.** Even as a fallback. GPL + file-size kills the serverless story.
- **No mobile SDKs at launch.** REST + the three SDKs cover mobile via their HTTP layer. Revisit after 100 paying customers.

## 5. Success metrics

| Metric | Target (90 days post-launch) | How measured |
|---|---|---|
| Developer signups (JWT keys minted) | 200 | Key ledger |
| Paid conversions (Studio tier) | 10 | Stripe subscriptions |
| API calls/day at peak | 50,000 | Upstash analytics |
| Median response latency | <150 ms | Vercel logs |
| SDK installs (TS + Python) | 1,000 combined | npm + PyPI weekly |
| Uptime | 99.9% | Vercel + external monitor |
| Dispute / refund rate | <1% | Stripe dashboard |

## 6. Requirements

### 6.1 API surface

43 endpoints, grouped:

- **Western** (9) — natal, extended, positions, houses, aspects, harmonic, dignities, asteroids, fixed stars
- **Vedic** (9) — chart, panchanga, dashas, chara-karakas, yogas, muhurta, ashtakavarga, shadbala, KP sublords
- **Hellenistic** (4) — profections, zodiacal releasing, planetary nodes, true node
- **Relational** (6) — transits, synastry, composite, progressions, solar returns, lunar returns
- **Electional** (5) — eclipses, prenatal eclipses, planetary hours, VOC moon, sunrise/sunset
- **Aggregators** (3) — daily, compatibility, events
- **Utility** (3) — geocode, OpenAPI spec, health

### 6.2 Auth

Stateless HS256 JWTs. Payload encodes:

- `sub` — key ID (used for denylist + analytics)
- `quotaPerMin` — per-key burst limit
- `quotaPerDay` — per-key sustained limit
- `scopes` — optional endpoint allowlist
- `exp` — expiration

Keys minted via a private CLI script (no public portal at v1). Revocation via `ASTROLOGY_API_DENYLIST` env var — a CSV of revoked `sub` values. No DB writes.

Justification: horizontal scaling without sticky sessions. Every serverless instance verifies the same JWT independently.

### 6.3 Rate limiting

Dual-window (per-minute + per-day). Dual-backend:

- **Upstash Redis** when `UPSTASH_REDIS_REST_URL` + `_TOKEN` are configured — global sliding window, analytics on
- **In-process token bucket** otherwise — per-instance fallback, no data loss on restart beyond the window

Response headers on every call:
- `x-ratelimit-limit-minute`, `x-ratelimit-remaining-minute`
- `x-ratelimit-limit-day`, `x-ratelimit-remaining-day`
- `retry-after` on 429

### 6.4 SDKs

| SDK | Runtime | Size | Registry | Notes |
|---|---|---|---|---|
| TypeScript | Node 18+, Bun, Deno, Edge | ~150 LOC | npm: `kriya-astrology` | Generic `fetchImpl` option for serverless runtimes |
| Python | 3.9+ | ~190 LOC | PyPI: `kriya-astrology` | stdlib-only via `urllib.request` |
| Go | 1.21+ | ~250 LOC | `github.com/omkarjaliparthi/kriya-go` | `net/http` only |

All three MIT-licensed. Server proprietary.

### 6.5 Pricing & packaging

| Tier | Price | Quota | Target |
|---|---|---|---|
| **Developer** | Free | 60 req/min · 1,000/day | Solo builders, prototyping |
| **Studio** | $49/mo | 600 req/min · 100,000/day | Post-PMF apps |
| **Scale** | Custom | Custom | Volume customers, SLA |

Billing routes through Stripe (existing parent-business infrastructure). No separate checkout build.

### 6.6 Docs

- `/` — marketing home with value prop + feature grid
- `/pricing` — three tiers + FAQ
- `/docs` — static endpoint reference (generated from OpenAPI)
- `/docs/api` — interactive Scalar explorer wired to `/openapi.json`
- `/dashboard` — live demo (free-tier natal chart) with origin-locked + IP-rate-limited demo endpoint

### 6.7 Accuracy disclosure

README + `/docs` publish accuracy targets with sources. No magical claims.

| Body | Accuracy | Source |
|---|---|---|
| Sun | ~36″ | Meeus ch. 25 |
| Moon | ~0.02° | ELP2000 (Meeus ch. 47, truncated) |
| Mercury–Neptune | ~1″ | VSOP87D (IMCCE FTP coefficients) |
| Pluto | ~1° | Standish Keplerian |

## 7. Risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Demo endpoint abused by scrapers | High | Medium | Origin check + IP rate limit + kill switch env var |
| JWT secret leakage | Low | Critical | Key rotation via new secret + denylist; no DB migration needed |
| Upstash outage | Medium | Low | In-process fallback keeps rate limiting functional |
| Accuracy complaint from expert astrologer | Medium | Low | Published accuracy table with citations; Meeus golden-file tests |
| Swiss Ephemeris users demanding parity | Low | Low | VSOP87 is the same underlying theory; document equivalence |
| Legal: commercial astrology claims | Low | Medium | Disclaimer + entertainment-use language in TOS |
| Competition from incumbent API refresh | Medium | Medium | First-principles engine + MIT SDKs + developer UX as moat |

## 8. Timeline

- **Day 1 (2026-04-15)** — engine rewrite (VSOP87 + ELP2000 + all traditions), 43 endpoints, auth, rate limiting, 3 SDKs, rebrand, pricing, docs — all shipped
- **Week 1** — monitor abuse patterns, refine demo guard thresholds, first paying customer
- **Month 1** — publish case study, RFC, one technical deep-dive blog post (build-vs-buy for ephemeris)
- **Month 3** — review metrics against P0/P1 targets, decide on developer portal build

## 9. Open questions

- **Pay-as-you-go?** — Studio tier is flat rate; consider metered overage for predictable scale path
- **White-label deployments?** — some prospects may want self-hosted; separate SKU with source-available license could unlock enterprise
- **Ephemeris download endpoint?** — some astronomy consumers may want raw positions in bulk; potential future product

## 10. References

- [Main repository](https://github.com/omkarjaliparthi/tuffys-ai-astrology) — engine, API, frontend (legacy repo name; product now trades as Kriya)
- [RFC · Home-grown ephemeris engine (build vs buy)](../rfcs/home-grown-ephemeris-engine.md) — technical decision doc for §6.1
- [Live API · Kriya](https://kriya.insightsbyomkar.com) · [Pricing](https://kriya.insightsbyomkar.com/pricing) · [API docs](https://kriya.insightsbyomkar.com/docs/api)
- Meeus, J. *Astronomical Algorithms*, 2nd ed. · Bretagnon & Francou, VSOP87 · Chapront-Touzé & Chapront, ELP2000 · Standish, JPL DE
