# Metrics methodology

> **Why this doc exists.** Headline numbers on a resume are unauditable by default. This document defines how each number I report was computed, what was in-scope, what was excluded, and what it does and does not mean. Reviewers are welcome to disagree with the methodology; they should not have to guess it.

---

## Numbers covered

1. **$1.2M+ portfolio scale**
2. **92% on-time delivery**
3. **58% faster MTTR (mean time to resolution)**
4. **95%+ SLA adherence across 100+ tracked risks**
5. **30% reduction in post-release defects**
6. **20% improvement in delivery predictability**

---

## 1 · $1.2M+ portfolio scale

**Context** · Agile/Scrum Project Manager role, Finest Minds Infotech, Bangalore · 01/2020 – 06/2022

**What is in-scope**

- Active client programs where I was accountable for delivery (not just coordinating)
- Summed across the concurrent program window at the point of peak concurrent value, not a career total
- Contract value (billable), not booked revenue or recognized revenue

**What is excluded**

- Pipeline or prospect engagements
- Programs I consulted on but did not run a team within
- Internal R&D projects that had no external contract

**What the number means**

- The programs I ran as Project Manager summed to ≥ $1.2M in active contract value at peak concurrency
- My accountability covered delivery across those programs: budget conversations, vendor selections, staffing trade-offs, escalations

**What it does not mean**

- Not revenue I generated
- Not a single-program ceiling; the largest individual program was smaller

---

## 2 · 92% on-time delivery

**Definition of on-time**

- A committed delivery was considered on-time if it met its **agreed-to date** at the start of the sprint/release cycle that contained the commitment
- Re-plans that occurred ≥ 2 weeks before the original date and were agreed-to by the stakeholder did not count as slips — the new date became the commitment
- Re-plans that occurred inside the 2-week window counted as slips, even if the stakeholder agreed, because the behavior we wanted to measure was *accurate prediction*, not *ability to renegotiate*

**Sample**

- 5 cross-functional teams running parallel sprint cadences
- 18 months of delivery data (after a 3-month warm-up in the role)
- ~240 committed deliveries in the sample (sprint-level and milestone-level combined)

**What the number means**

- 92% of committed deliveries met the commitment made at the start of their cycle
- Calculated as on-time / (on-time + slipped). Cancelled items are excluded from the denominator.

**What it does not mean**

- Not a quality metric — late-and-good vs. on-time-and-broken are both inside this number; quality is captured separately under "post-release defects"
- Not applicable across all of my career equally — this number is specific to that program portfolio

---

## 3 · 58% faster MTTR

**Context** · Business Analyst, Shivshanti Inc (Red Roof Inn franchise), NY · 10/2023 – 11/2025. Incident response and cross-team RCA programs.

**Baseline**

- MTTR measured over the 12 months preceding the RCA program's introduction
- Source · internal ticket system timestamps (open → resolved)
- Only incidents that were formally opened count · informal escalations excluded because they had no consistent timestamp

**Post-change window**

- 12 months after the RCA program was established and adopted across teams

**Calculation**

- `(Baseline MTTR − Post-change MTTR) / Baseline MTTR = % improvement`
- Rounded to the nearest whole percent

**What drove the change** (not the number itself, but the reader will ask)

- Cross-team RCA cadence — incidents that previously ping-ponged between functional owners now had a single accountable RCA owner
- Runbook discipline — the first five repeat incidents got runbooks; subsequent recurrences were triaged in minutes rather than hours
- A pre-defined severity ladder — fewer minutes spent debating "how bad is this."

**What the number does not mean**

- Not an engineering MTTR in the SRE sense; this is an operations + support context with human steps in the loop
- Not necessarily replicable in a different environment with different tooling

---

## 4 · 95%+ SLA adherence across 100+ tracked risks

**Context** · Same role as #3. Risk register maintained in ServiceNow.

**Definition of SLA**

- Each tracked risk had an owner and an SLA-defined review/mitigation cadence (weekly, monthly, or on-trigger)
- A risk was *in adherence* for a period if its scheduled review happened on time **and** its mitigation either advanced or was explicitly documented as no-progress-with-reason

**Sample** · 100+ unique risks logged during the observation period; the 95%+ rate is the median weekly adherence across the sample

**What the number does not mean**

- Not that 95% of risks were *closed* — adherence is about the process, not the outcome
- A risk that stays open indefinitely but is reviewed on cadence counts as in-adherence

---

## 5 · 30% reduction in post-release defects

**Context** · Finest Minds portfolio

**Baseline** · Defects logged in the 4 sprints following a release, averaged across 6 releases pre-change

**Post-change window** · Same definition, averaged across 6 releases after the change

**Drivers**

- Pre-release checklist adoption (equivalent to the launch-checklist template in this repo, one era earlier)
- RAID review moved from end-of-sprint to mid-sprint · risks surfaced earlier in the cycle

**What the number does not mean**

- Not zero-defect · defects still shipped; their rate dropped
- Not evenly distributed across severity · the improvement was stronger on P3/P4 defects than on P1/P2; the latter require different interventions

---

## 6 · 20% improvement in delivery predictability

**Definition of predictability**

- Ratio of `actual-effort / estimated-effort` at the sprint level, averaged across teams
- Predictability improves as this ratio approaches 1.0 *and* its variance drops

**Sample** · 12 months, 5 teams, sprint-level

**Calculation**

- Pre-change variance of the ratio compared to post-change variance
- 20% is the reduction in variance · teams got closer to their own estimates more consistently

**What the number does not mean**

- Not that teams got faster — velocity is orthogonal to predictability
- Not that estimates got smaller — estimates got more *accurate*, which is different

---

## Why write this down

1. **Numbers without methodology are decoration.** Any number in a bullet point should be defensible in an interview; methodology docs are how they become defensible.
2. **Self-accountability.** Writing the definitions down is how I keep myself from inflating or drifting the numbers over time.
3. **Transferability.** A reader can tell whether my definition of on-time is *their* definition of on-time, and discount accordingly.
4. **Interview signal.** Senior PM/TPM candidates are expected to think about measurement design, not just report measurements.

---

*Open to good-faith challenges on any of these definitions. If a reviewer proposes a tighter definition that I can recompute against, I will.*
