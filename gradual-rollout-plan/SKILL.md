---
name: gradual-rollout-plan
description: Guides a Product Manager through building a structured gradual release plan based on the Idira Gradual Release Framework. Use when a PM says anything like "build a gradual rollout plan", "build me a rollout strategy", "create a release plan", "rollout strategy", "release strategy", "how should I roll this out", "canary plan", "feature rollout", or mentions NFI, release rings, LaunchDarkly flags, or customer exposure waves.
---

# Gradual Rollout Plan

Generates a structured release plan following the Idira Gradual Release Framework: infer signals from the PRD, classify the release path, map rings, build the R3 cohort plan, and provide LaunchDarkly setup steps.

---

## Step 1 — Read and Infer from the PRD

Read the PRD in full. Infer every signal below from the content. Do not ask the PM for anything you can determine yourself.

| Signal | What to look for |
|--------|-----------------|
| **NFI** | "NFI" label, "new feature", "not previously available", net-new capability with no prior version |
| **Impact** | Scope of affected users/tenants, "critical path" / "core workflow" language, data sensitivity, risk of data loss or service disruption |
| **Rollback complexity** | Schema migrations, data writes, third-party dependencies, customer comms required, irreversible state → Coordinated or Irreversible. Flag flip only → Easy |
| **Region constraints** | GDPR, FedRAMP, data residency, geography-specific requirements |
| **Service constraints** | Named services, integrations, or components the feature depends on or modifies |
| **Earliest start** | Sprint dates, milestone dates, "ready for QA" signals |
| **Target GA** | Committed launch date, OKR deadline, release milestone |

**After reading**, present your inferences — do not ask open questions:

```
Based on the PRD, here is how I've read this release:

- NFI:               [Yes / No — reason]
- Impact:            [High / Medium / Low — reason]
- Rollback:          [Easy / Coordinated / Irreversible — reason]
- Region constraints:[None / list — reason]
- Service constraints:[None / list — reason]
- Earliest start:    [date or "not found in PRD"]
- Target GA:         [date or "not found in PRD"]

→ Release path: [LIGHTWEIGHT / STANDARD / HIGH-TOUCH]
```

**Fallback — only if a signal cannot be inferred:** ask a targeted, closed question for that specific signal only. Examples:

- Impact missing → "I couldn't determine impact from the PRD. Is this feature High, Medium, or Low impact on customers?"
- Rollback missing → "The PRD doesn't describe rollback. If this goes wrong, is it a simple flag flip (Easy), does it need migrations or customer comms (Coordinated), or is it irreversible?"
- Dates missing → "What is the target GA date?"

Do not ask about signals you already resolved from the PRD.

---

## Step 2 — Classify the Release Path

```
NFI = Yes           →  HIGH-TOUCH
OR Impact = High
OR Rollback = Irreversible

Impact = Medium     →  STANDARD
OR Rollback = Coordinated

All others          →  LIGHTWEIGHT
```

---

## Step 3 — Map Required Rings

| Path | Mandatory rings | R&D owns | PM owns |
|------|----------------|----------|---------|
| **LIGHTWEIGHT** | R0 → R4 | R0 | R4 |
| **STANDARD** | R0 → R3 → R4 | R0 | R3, R4 |
| **HIGH-TOUCH** | R0 → R1 → R2 → R3 → R4 | R0, R1 | R2, R3, R4 |

- **R0** Internal QA — engineering/QA environments
- **R1** Customer Zero / Dogfooding — internal teams
- **R2** Customer One — selected Early Access customers (via NFI process)
- **R3** Production Canary — Test Tenants → live cohorts via LaunchDarkly flags
- **R4** General Availability — all eligible production tenants

---

## Step 4 — Build the R3 Cohort Plan (Standard & High-Touch only)

R3 always starts with **Test Tenants** first. Then sequence live tenants by ARR:

| Wave | Cumulative % | Targeting |
|------|-------------|-----------|
| 0 | — | Test Tenants (customer-purchased sandboxes) |
| 1 | 5% | Lowest ARR, small tenant count |
| 2 | 15% | Low-to-mid ARR |
| 3 | 35% | Mid ARR |
| 4 | 65% | Mid-to-high ARR |
| 5 | 100% | All remaining eligible tenants |

Adjustments:
- **Service constraints** → add service-filter targeting per wave
- **Region constraints** → add region filter to early waves, remove for wave 4–5
- **Short timeline** → collapse waves (e.g. 5% → 35% → 100%)
- **High ARR sensitivity** → hold high-ARR tenants to wave 4 with extended bake time

For each wave include: %, cohort description, go/no-go gate (metric + bake time), owner.

---

## Step 5 — Output the Full Release Plan

Produce the plan using this template:

```markdown
# Gradual Release Plan — [Feature Name]

**Release path:** [LIGHTWEIGHT / STANDARD / HIGH-TOUCH]
**Earliest start:** [date]  |  **Target GA:** [date]

---

## How I read this release

| Signal | Value | Reasoning |
|--------|-------|-----------|
| NFI | Yes/No | [one line] |
| Impact | High/Medium/Low | [one line] |
| Rollback | Easy/Coordinated/Irreversible | [one line] |
| Region constraints | None / [list] | [one line] |
| Service constraints | None / [list] | [one line] |

---

## Required Rings

| Ring | Stage | Owner | Entry gate | Exit gate |
|------|-------|-------|------------|-----------|
| R0 | Internal QA | R&D | Merge to main | QA sign-off |
| R1 | Dogfooding | R&D | R0 complete | Internal sign-off *(High-Touch only)* |
| R2 | Customer One | PM | NFI process complete | Customer feedback reviewed *(High-Touch only)* |
| R3 | Production Canary | PM | R2 complete | 100% wave stable |
| R4 | General Availability | PM | R3 100% wave complete | PM go/no-go |

*(Include only the rings required for the chosen path)*

---

## R3 Cohort Plan

**First:** Test Tenants — confirm safe activation before any live tenants.

| Wave | % | Who | Targeting | Go/No-Go Gate |
|------|---|-----|-----------|---------------|
| 0 | — | Test Tenants | External test tenants | No incidents for 48 h |
| 1 | 5% | Low ARR | [criteria] | [metric] stable for [bake time] |
| 2 | 15% | Low–mid ARR | [criteria] | [metric] stable for [bake time] |
| 3 | 35% | Mid ARR | [criteria] | [metric] stable for [bake time] |
| 4 | 65% | Mid–high ARR | [criteria] | [metric] stable for [bake time] — extended |
| 5 | 100% | All eligible | Remove filters | [metric] stable for [bake time] |

---

## LaunchDarkly Setup

**1. Create the feature flag**
- Flag key: `[feature-name]-rollout` (lowercase, hyphens)
- Type: Boolean
- Default (off): false for all environments

**2. Configure environments**
- `internal-qa` → R0: enable for R&D QA team
- `dogfooding` → R1: enable for internal employee tenants *(High-Touch only)*
- `early-access` → R2: enable for named Customer One tenants *(High-Touch only)*
- `production` → R3: targeting rules below

**3. Production targeting rules (R3 waves)**

Wave 0 — Test Tenants:
- Rule: `tenant_type = "test"` → serve true

Wave 1 — 5%:
- Rule: `tenant_type = "live" AND arr_tier = "low"` → serve true to 5% rollout

Wave 2 → 5: Increment the rollout percentage and expand the `arr_tier` filter per wave.
Remove the `arr_tier` filter entirely at wave 5 (100%).

If service constraints apply:
- Add `AND deployed_services CONTAINS "[service-name]"` to each rule.

If region constraints apply:
- Add `AND region IN ["[region]"]` to early waves; remove for wave 4–5.

**4. Gates between waves**
- Monitor error rate, latency (p99), and support tickets for the bake period.
- Only expand to the next wave after the go/no-go criteria are met.
- To pause or roll back: set the production flag to false — all tenants immediately revert.

**5. Single toggle**
- R&D must implement one flag key across all affected components.
- Confirm with R&D that all components read the same flag before R3 starts.

---

## Owners & Responsibilities

- **R&D** — Implements the flag, owns R0–R1, validates single-toggle coverage
- **PM** — Owns customer exposure (R2–R4), signs each wave gate, updates cohort plan
- **Product Ops** — Reviews plan against mandatory guidelines before R3 starts

## Open Questions / Assumptions

- [List facts assumed or still to be confirmed with R&D / BI]
```

---

## Example

See [reference.md](reference.md) for a worked example (NFI, High Impact, 3-month GA target) showing a completed plan with filled-in cohort table and LaunchDarkly rules.
