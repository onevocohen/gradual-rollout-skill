---
name: gradual-rollout-plan
description: Guides a Product Manager through building a structured gradual release plan based on the Idira Gradual Release Framework. Use when a PM asks to create a rollout plan, release plan, gradual rollout, canary plan, or release strategy for a feature. Also use when the PM mentions NFI, release rings, LaunchDarkly flags, or customer exposure waves.
---

# Gradual Rollout Plan

Generates a structured release plan following the Idira Gradual Release Framework: classify the release path, identify mandatory rings, and produce a tailored R3 cohort plan.

## Step 1 — PM Intake

Ask the PM for the following (check the PRD first; only ask for what's missing):

| # | Question | Accepted values |
|---|----------|-----------------|
| 1 | **Feature name / PRD link** | Free text |
| 2 | **Is this a New Feature Introduction (NFI)?** | Yes / No |
| 3 | **Customer-facing impact** | High / Medium / Low |
| 4 | **Rollback complexity** | Easy (flag flip) / Coordinated (requires migration/comms) / Irreversible |
| 5 | **Region constraints** | None / Specific regions (list them) |
| 6 | **Service constraints** | None / Specific services (list them) |
| 7 | **Earliest start date** | Date or "Now" |
| 8 | **Target GA date** | Date or timeframe (e.g. "3 months") |

If the PRD answers any question, use that answer and note it. Do not ask for information already supplied.

---

## Step 2 — Classify the Release Path

Apply this decision tree in order:

```
NFI = Yes  →  HIGH-TOUCH
OR  Impact = High
OR  Rollback = Irreversible

Impact = Medium  →  STANDARD
OR  Rollback = Coordinated

All others  →  LIGHTWEIGHT
```

---

## Step 3 — Map Required Rings

| Path | Mandatory rings | R&D owns | PM owns |
|------|----------------|----------|---------|
| **LIGHTWEIGHT** | R0 → R4 | R0 | R4 |
| **STANDARD** | R0 → R3 → R4 | R0 | R3, R4 |
| **HIGH-TOUCH** | R0 → R1 → R2 → R3 → R4 | R0, R1 | R2, R3, R4 |

Ring definitions:
- **R0** Internal QA — engineering/QA environments
- **R1** Customer Zero / Dogfooding — internal teams
- **R2** Customer One — selected Early Access customers (via NFI process)
- **R3** Production Canary — Test Tenants → live cohorts via LaunchDarkly flags
- **R4** General Availability — all eligible production tenants

---

## Step 4 — Build the R3 Cohort Plan (Standard & High-Touch only)

R3 always starts with **Test Tenants** (customer-purchased sandboxes). Then sequence live tenants:

**Default exposure wave template:**

| Wave | Cumulative % of eligible live tenants | Targeting criteria |
|------|---------------------------------------|--------------------|
| 1 | 5% | Lowest ARR, small tenant count |
| 2 | 15% | Low-to-mid ARR |
| 3 | 35% | Mid ARR |
| 4 | 65% | Mid-to-high ARR |
| 5 | 100% | All remaining eligible tenants |

Apply these adjustments based on PM inputs:
- **Service constraints** → add service-filter targeting to each wave
- **Region constraints** → add region filter to early waves, remove for later waves
- **Short timeline** → consider collapsing waves (e.g. 5% → 35% → 100%)
- **High ARR sensitivity** → add high-ARR tenants to wave 4 only, with extended bake time

For each wave, include: target %, cohort description, gate criteria (metrics + bake time), owner.

---

## Step 5 — Output the Release Plan

Use this template exactly:

```markdown
# Gradual Release Plan — [Feature Name]

**Release path:** [LIGHTWEIGHT / STANDARD / HIGH-TOUCH]
**Earliest start:** [date]  |  **Target GA:** [date]

---

## Required Rings

| Ring | Stage | Owner | Entry gate | Exit gate |
|------|-------|-------|------------|-----------|
| R0 | Internal QA | R&D | Merge to main | QA sign-off |
| [include only rings in the mandatory path] |
| R4 | General Availability | PM | R3 100% wave complete | PM go/no-go |

---

## R3 Cohort Plan  _(Standard & High-Touch only)_

**First:** Test Tenants — confirm safe activation before live tenants.

| Wave | % | Who | Targeting | Gate |
|------|---|-----|-----------|------|
| 0 | — | Test Tenants | External test tenants | No incidents 48 h |
| 1 | 5% | Low ARR | [criteria] | [metric] for [bake time] |
| 2 | 15% | Low–mid ARR | [criteria] | [metric] for [bake time] |
| 3 | 35% | Mid ARR | [criteria] | [metric] for [bake time] |
| 4 | 65% | Mid–high ARR | [criteria] | [metric] for [bake time] |
| 5 | 100% | All eligible | Remove filters | [metric] for [bake time] |

---

## Owners & Responsibilities

- **R&D** — Implements LaunchDarkly flags, owns internal rings (R0–R1)
- **PM** — Defines cohort plan, owns customer exposure (R2–R4), signs go/no-go gates
- **Product Ops** — Reviews plan against mandatory guidelines

## Open Questions / Assumptions

- [List any facts assumed or still to be confirmed]
```

---

## Reference

For the full framework rationale, industry examples, and LaunchDarkly validation checklist, see [reference.md](reference.md).
