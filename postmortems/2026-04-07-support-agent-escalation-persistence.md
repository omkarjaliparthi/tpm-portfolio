# Postmortem — Support Agent Escalation Reverting to Tier-1

**Date:** 2026-04-07
**Severity:** High (user-facing, recurring, no data loss)
**Author:** Omkar Jaliparthi
**Status:** Resolved

---

## Summary

Escalated support chat sessions were reverting to Tier-1 agents after the next user message. Users who had been escalated to the senior agent (Nadia, Tier-2, Anthropic-backed) would find themselves talking to a Tier-1 agent on their next turn — same session, no UI indicator, no visible handoff back. Affected all escalated sessions for an unknown window between v2.0.7 deploy and discovery.

## Impact

- **Users affected:** all users who triggered escalation (3 known from logs; true count may be higher)
- **Data loss:** none — messages and context persisted correctly
- **Business impact:** trust erosion for users who specifically asked for / needed a senior agent. No refund requests tied directly to this.

## Timeline *(all times UTC)*

| Time | Event |
|---|---|
| 2026-04-07 14:22 | v2.0.7 deployed to production |
| 2026-04-07 16:48 | First escalation triggered in production (logs) |
| 2026-04-07 16:49 | Same session — next message routed back to Tier-1 agent |
| 2026-04-07 18:10 | Self-QA during ops review spots the pattern |
| 2026-04-07 18:22 | Root cause identified |
| 2026-04-07 18:51 | Fix deployed in v2.0.7-hotfix |
| 2026-04-07 19:00 | Affected sessions manually backfilled to correct agent_id |

## Root cause

The `chat_messages` insert path had a stale default value. When a new message was written to an escalated session, the insert did not explicitly set `escalated_to_agent` — leaving the column to fall back to `NULL` via the default, which the agent-selection logic read as "not escalated" and routed to the weekly Tier-1 agent.

The escalation was being **stored correctly on escalation event**, but **overwritten on the next message** because the subsequent insert used a stale default rather than preserving the session-level escalation state.

## Contributing factors

1. **No escalation-flow staging test.** The escalation path had been manually tested end-to-end once; no automated smoke test covered the "after N more messages" phase.
2. **Default-value ambiguity in migration.** The migration added `escalated_to_agent` with `DEFAULT NULL` rather than making the column explicitly session-derived at read time.
3. **Manual production monitoring.** I was the only set of eyes on escalation flows; a dashboard showing "escalated sessions with recent messages" would have surfaced the issue faster.

## What went well

- **Same-day detection and fix.** Window of exposure was under 4 hours.
- **Versioned releases made the rollback option trivial** even though we didn't need it — the hotfix went out as v2.0.7-hotfix, cleanly tagged.
- **Blameless attitude.** I was solo on this, but the instinct to treat it as a system problem (defaults, tests, observability) rather than a personal failure shaped the corrective actions.

## What went badly

- **Launched v2.0.7 with insufficient smoke testing** of the full escalation lifecycle.
- **Observability gap** — no alert would've caught this without manual review.

## Corrective actions

| Action | Owner | Due | Status |
|---|---|---|---|
| Add "escalate + 2 more messages" smoke test to pre-launch checklist | Omkar | 2026-04-07 | ✅ Done |
| Migration fix: make `escalated_to_agent` explicit in every insert path | Omkar | 2026-04-07 | ✅ Done |
| Ops dashboard: "escalated sessions last 24h" with agent-per-message timeline | Omkar | 2026-04-21 | 📅 Queued |
| Weekly review: top 5 escalated sessions + outcome labels | Omkar | Ongoing | 🔄 Started 2026-04-14 |

## Lessons

1. **Default values in migrations are a latent bug surface.** For state-tracking columns, prefer explicit inserts over defaults.
2. **A "single manual QA" is not QA.** If a flow matters, it needs an automated test that runs on every deploy.
3. **Observability is a product feature for ops.** The dashboard should have existed before the bug could.

---

*Postmortem format: adapted from Google SRE blameless postmortem template — Summary → Impact → Timeline → Root cause → Factors → What went well/badly → Corrective actions → Lessons. Solo-scale, but same rigor.*
