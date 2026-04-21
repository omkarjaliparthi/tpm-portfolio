# Postmortem · API rate limiter silently fell back to in-memory

**Date** · 2026-03-18
**Severity** · Medium · user-facing drift, no data loss, revenue unaffected
**Author** · Omkar Jaliparthi
**Status** · Resolved

---

## Summary

The public API's rate limiter silently failed over from Upstash Redis to the in-process fallback during a ~40-minute window. Per-process counters do not share state across serverless invocations, so customers who hit their quota on one instance could bypass it on another. No customer exceeded plan quotas by a material amount during the window; the drift was caught by routine log review, not alerting.

## Impact

- **Customers affected** — any paid-tier caller during the window (low single-digit count based on request logs)
- **Over-quota requests** — an audit found overages well below the threshold for a billing correction; no customer was charged or credited
- **Revenue** — no measurable change
- **Trust** — internal only; customers were not notified because there was no externally visible incorrect behavior

## Timeline · UTC

| Time | Event |
|---|---|
| 09:12 | Upstash region briefly returns 5xx on a subset of reads |
| 09:12 | Limiter catches the error and falls back to in-process counters — by design |
| 09:13 | Upstash recovers; fallback does not switch back automatically |
| 09:52 | Routine log review spots an unusual concentration of `limiter.fallback.active` log lines |
| 09:54 | Manual redeploy forces fresh instances; limiter returns to Redis-backed mode |
| 10:30 | Audit complete; overage impact measured, determined immaterial |

## Root cause

Two faults compounded:

1. **Fallback was one-way.** On the first Upstash error the limiter set an in-memory flag to use the local counter. No timer or health check ever flipped the flag back.
2. **No alert on the fallback flag.** The fallback was logged, but no metric or alert existed on *"fallback is currently active."*

The Upstash hiccup was the trigger; the stuck fallback was the incident.

## Contributing factors

1. **Stateless serverless + per-process flag.** A stateful flag in a stateless environment means the flag is also per-instance. Some instances recovered, others didn't, depending on whether they had seen the original failure.
2. **"Fallback is working" feels like success.** In tests, a fallback that kicks in and requests continue to serve looks green. The failure mode — fallback that doesn't switch back — is harder to test for.
3. **No synthetic probe.** A probe that dials Upstash directly would have shown it was healthy, independent of the limiter's opinion.

## What went well

- **Observability caught it** — manual, but it caught it
- **Customer impact was bounded** because the blast radius is clipped by plan quotas on a short time window
- **Redeploy was sufficient** — no data cleanup needed; rate limit counters reset cleanly by design

## What went badly

- **No first-class alert** on `limiter.fallback.active`
- **No health-check path** to re-arm the primary backend after transient failure
- **Incident would have grown** if the audit had found material overage — the fix is deploy-gated, not real-time

## Corrective actions

| Action | Owner | Due | Status |
|---|---|---|---|
| Add health-check loop · re-attempt Upstash every 60s, flip flag back on success | Omkar | 2026-03-20 | ✅ Done |
| Emit metric `limiter.fallback.active` · alert if > 5 minutes | Omkar | 2026-03-22 | ✅ Done |
| Add synthetic probe · ping Upstash on a schedule, independent of the app | Omkar | 2026-04-05 | ✅ Done |
| Document "stateful flags in stateless runtimes" as an ADR-worthy trap | Omkar | 2026-04-19 | 📅 Queued |

## Lessons

1. **Fallbacks need round-trips, not one-way doors.** Any "use the backup" branch needs a timer that tries the primary again.
2. **"Log the event" is not "alert on the state."** A metric for *how long* the fallback has been active matters more than the log line that it started.
3. **Stateful behavior in stateless runtimes needs careful thought.** Per-process flags are a footgun; either externalize the state or make the flag self-heal.
