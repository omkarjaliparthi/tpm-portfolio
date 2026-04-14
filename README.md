<h1 align="center">TPM × PM Portfolio</h1>

<p align="center">
  <b>Real artifacts from shipped programs — PRDs, RFCs, launch checklists, postmortems, status rollups.</b><br/>
  <i>Curated by <a href="https://github.com/omkarjaliparthi">Omkar Jaliparthi</a> — Product & Program Leader · San Jose</i>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-actively_maintained-success?style=for-the-badge" />
  <img src="https://img.shields.io/badge/audience-recruiters_&_hiring_managers-6E56CF?style=for-the-badge" />
</p>

---

## Why this repo exists

Most PM/TPM resumes tell you the *outcome* — "shipped X," "drove Y% improvement." This repo shows the **artifacts** behind those outcomes: the documents I actually used to run programs, align stakeholders, make decisions, and recover from incidents.

Everything here is either redacted from real programs I've run or built as templates I use today. Where companies/product details are sensitive, names are anonymized — structure and craft are preserved.

---

## 📂 Contents

### 📝 Product & strategy
- **[PRDs](./prds/)** — Product requirement docs with clear problem framing, user segments, success metrics, and scoping decisions
- **[One-pagers](./one-pagers/)** — Pre-PRD narrative docs used for alignment before writing user stories
- **[Pricing & packaging](./pricing/)** — Subscription tiers, credits model, refund policy — from `insights-by-omkar`

### 🏗️ Technical program management
- **[RFCs / design docs](./rfcs/)** — Technical design docs for multi-team architecture decisions
- **[RAID logs](./raid/)** — Risk, Assumption, Issue, Dependency tracking templates and examples
- **[Release governance](./release-governance/)** — Go/no-go frameworks, launch checklists, UAT sign-off templates

### 🚨 Incident & postmortem
- **[Postmortems](./postmortems/)** — Blameless incident reviews with timelines, contributing factors, corrective actions
- **[RCA templates](./rca/)** — Root-cause analysis frameworks I've used in production

### 📊 Operating rhythm
- **[Status rollups](./status-rollups/)** — Weekly/bi-weekly program status templates for exec + stakeholder audiences
- **[Sprint cadence](./sprint-cadence/)** — Planning, retro, and delivery-metric templates (velocity, cycle time, defect density)
- **[OKR planning](./okrs/)** — Goal-setting and tracking templates

### 🎯 Launch artifacts
- **[Pre-launch checklist](./launch-checklists/insights-by-omkar.md)** — Real 13-section checklist used to launch a production AI SaaS (env vars, payments, DNS, compliance, monitoring, smoke tests)
- **[Changelog discipline](./changelogs/)** — Versioned release patterns (`v0.01` → `v2.0.7`) I use for solo and team ships

---

## 🔑 Programs these artifacts come from

| Program | Context | Role |
|---|---|---|
| **Insights by Omkar** | Production AI SaaS, solo-shipped in 6 weeks — dual payments, multi-tier support, compliance | Founder / Product / Program / Engineering |
| **Finest Minds portfolio** | $1.2M+, 5 cross-functional teams, 92% on-time delivery | Project Manager (Agile/Scrum) |
| **MediLink** | Healthcare records platform, 10-person team, 6 Agile sprints | Project Manager |
| **Shivshanti Ops** | Franchise operations + incident response, 58% faster MTTR | Business Analyst |

---

## 💡 How I write docs

1. **Start from the customer problem**, not the feature. If I can't write the problem statement in one paragraph, I'm not ready to write the PRD.
2. **Explicit success metrics**, owned and dated. "Improve engagement" is not a metric.
3. **Risks are commitments, not footnotes.** Every RAID entry has an owner, due date, and mitigation.
4. **Decisions get written down.** RFCs, ADRs, decision logs — async > sync for durable alignment.
5. **Postmortems are blameless.** What the system let happen, not who missed what.

---

<p align="center">
  <b>Hiring?</b> <a href="mailto:Jaliparthiomkar03@gmail.com">Jaliparthiomkar03@gmail.com</a> · <a href="https://github.com/omkarjaliparthi">github.com/omkarjaliparthi</a>
</p>
