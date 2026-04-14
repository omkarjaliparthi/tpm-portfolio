# Postmortem · Support Agent Escalation Reverting to Tier-1

**Date** · 2026-04-07
**Severity** · High · user-facing, recurring, no data loss
**Author** · Omkar Jaliparthi
**Status** · Resolved

---

## Summary

Escalated support sessions reverted to Tier-1 on the next user message. Users escalated to the senior agent (Nadia, Tier-2, Anthropic) would find themselves back with a Tier-1 agent — same session, no UI indicator, no visible handoff back. Affected all escalated sessions for an unknown window between v2.0.7 deploy and discovery.

## Impact

- **Users affected** — all who triggered escalation (3 confirmed; true count may be higher)
- **Data loss** — none; messages and context persisted
- **Business** — trust erosion for users who specifically needed a senior agent. No refund requests tied to this.

## Timeline · UTC

| Time | Event |
|---|---|
| 14:22 | v2.0.7 deployed |
| 16:48 | First escalation in production |
| 16:49 | Next message routed back to Tier-1 |
| 18:10 | Ops review spots the pattern |
| 18:22 | Root cause identified |
| 18:51 | Fix deployed as v2.0.7-hotfix |
| 19:00 | Affected sessions backfilled |

## Root cause

The `chat_messages` insert path had a stale default for `escalated_to_agent`. New messages written to an escalated session inherited `NULL` via default, which the agent-selection logic read as "not escalated" and routed to the weekly Tier-1.

Escalation was **stored correctly** on the escalation event. It was **overwritten on the next message** because the subsequent insert used a stale default instead of preserving session state.

## Contributing factors

1. **No escalation-flow staging test.** Manual end-to-end test once; no automated smoke test for "after N more messages".
2. **Default-value ambiguity in migration.** `escalated_to_agent` added with `DEFAULT NULL` instead of being derived from session at read time.
3. **Manual production monitoring.** A dashboard for "escalated sessions with recent messages" would have surfaced it faster.

## What went well

- **Same-day detection + fix.** Exposure window under 4 hours.
- **Versioned releases made rollback trivial.** We didn't need it — hotfix went out as v2.0.7-hotfix, cleanly tagged.
- **Blameless framing.** Solo, but treated as a system problem — defaults, tests, observability — not personal failure.

## What went badly

- **Insufficient smoke testing** of the full escalation lifecycle pre-launch
- **Observability gap** — no alert would have caught this without manual review

## Corrective actions

| Action | Owner | Due | Status |
|---|---|---|---|
| Add "escalate + 2 messages" smoke test to pre-launch checklist | Omkar | 2026-04-07 | ✅ Done |
| Migration fix · make `escalated_to_agent` explicit in every insert path | Omkar | 2026-04-07 | ✅ Done |
| Ops dashboard · escalated sessions last 24h with per-message agent timeline | Omkar | 2026-04-21 | 📅 Queued |
| Weekly review · top 5 escalated sessions + outcome labels | Omkar | Ongoing | 🔄 Started 2026-04-14 |

## Lessons

1. **Migration defaults are a latent bug surface.** For state-tracking columns, prefer explicit inserts over defaults.
2. **"One manual QA" is not QA.** If a flow matters, it needs an automated test on every deploy.
3. **Observability is a product feature for ops.** The dashboard should have existed before the bug could.
