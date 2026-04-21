# ADR 0002 · TypeScript over Python for the API layer

**Status** · Accepted · 2026-02-18
**Author** · Omkar Jaliparthi
**Program** · Insights Astrology API

---

## Context

The astronomy API ships 40+ endpoints plus SDKs. Language choice for the server layer needed to satisfy:

1. Strong typing at API boundaries — request/response contracts enforced in code
2. One language end-to-end when the same person owns the SaaS front-end
3. Ecosystem fit for OpenAPI generation, rate limiting, serverless deploys

## Options

### A · Python (FastAPI)

- Mature for numeric work; the astronomy community writes Python
- Pydantic for contracts is excellent
- Serverless cold starts heavier; deploy surface diverges from the Next.js SaaS

### B · TypeScript (Next.js API routes)

- Shares runtime, toolchain, and mental model with the SaaS
- OpenAPI 3.1 tooling mature; type inference from schema is first-class
- Numeric computation requires care — no NumPy equivalent, but the engine is deterministic and doesn't need one

### C · Go

- Excellent perf, single-binary deploys
- Ecosystem fit for numeric + OpenAPI weaker than TS; SDK ergonomics lose to either alternative
- Doubles the language count in the portfolio for little gain on a hot path that is not CPU-bound at this scale

## Decision

**Option B — TypeScript on Next.js API routes.**

## Consequences

**Accepted:**
- Numeric code requires manual attention to floating-point behavior and array allocation — no NumPy safety net
- Some astronomy reference implementations must be ported rather than vendored

**Won:**
- One runtime across SaaS + API + SDKs — shared linting, testing, deploy pipeline
- Zod at the edge; request validation and OpenAPI descriptions generated from the same schema
- Vercel serverless functions deploy fast with small cold starts when the engine is kept lazy
- Type-safe client SDKs can be generated from the server types without an IDL translation pass

## Exit criteria

Revisit if any hold:

- A required computation is >5× faster in a native language and is on a customer hot path
- Cold start times regress past the SLA after dependency growth
- The team grows past one person and a second language makes ownership boundaries cleaner

## Related

- [ADR 0003 · Monorepo split · Netra extraction](./0003-monorepo-split-netra-extraction.md)
- [RFC · Home-Grown Ephemeris Engine · Build vs Buy](../rfcs/home-grown-ephemeris-engine.md)
