# Architecture Decision Records

Short, numbered records for decisions that shape the system. ADRs are the *small* siblings of RFCs — one page, one decision, one rationale.

## Format

Each ADR follows:

- **Status** — Proposed · Accepted · Superseded · Deprecated
- **Context** — why this decision is in front of us now
- **Options** — what we considered
- **Decision** — what we chose
- **Consequences** — the trade we accepted

## When an ADR vs an RFC

| Use an ADR | Use an RFC |
|---|---|
| Single-author decision | Multi-team decision |
| ~1 page | ~5–15 pages |
| Reversible within days | Reversible in months or not at all |
| Framework / library / convention | Architecture / product / platform |

## Index

- [0001 · Supabase over self-hosted Postgres](./0001-supabase-over-self-hosted-postgres.md)
- [0002 · TypeScript over Python for the API layer](./0002-typescript-over-python-api-layer.md)
- [0003 · Monorepo split · Netra extraction](./0003-monorepo-split-netra-extraction.md)
- [0004 · Release cadence philosophy](./0004-release-cadence-philosophy.md)
- [0005 · Pricing tier psychology](./0005-pricing-tier-psychology.md)
