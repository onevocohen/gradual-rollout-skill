# Gradual Rollout Plan — Reference

## Worked Example — NFI, High Impact, 3-month GA

**Scenario:** A PM pastes a PRD for a new identity verification feature. The PRD says "not previously available to customers", "affects all login flows", "requires a database schema migration", and has a target launch in Q1.

**Step 1 — Inferences:**
```
- NFI:               Yes — "not previously available to customers"
- Impact:            High — "affects all login flows"
- Rollback:          Coordinated — "database schema migration" required
- Region constraints:None — no regulatory language found
- Service constraints:Identity Service, Auth Service — named in PRD
- Earliest start:    Now (no blocking dependencies mentioned)
- Target GA:         3 months (Q1 target)

→ Release path: HIGH-TOUCH (NFI = Yes)
```

**Step 2 — Required Rings:** R0 → R1 → R2 → R3 → R4

**Step 3 — R3 Cohort Plan:**

| Wave | % | Who | Targeting | Go/No-Go Gate |
|------|---|-----|-----------|---------------|
| 0 | — | Test Tenants | `tenant_type = "test"` | No incidents for 48 h |
| 1 | 5% | Low ARR | `arr_tier = "low" AND deployed_services CONTAINS "identity"` | Error rate <0.1% for 48 h |
| 2 | 15% | Low–mid ARR | `arr_tier IN ["low","mid-low"] AND deployed_services CONTAINS "identity"` | Error rate <0.1% for 48 h |
| 3 | 35% | Mid ARR | `arr_tier = "mid"` | Error rate <0.1%, p99 latency <5% regression for 72 h |
| 4 | 65% | Mid–high ARR | `arr_tier IN ["mid","mid-high"]` | 1 week bake, <3 tickets per 1,000 tenants |
| 5 | 100% | All eligible | Remove all filters | 48 h stable → PM go/no-go |

**Step 4 — LaunchDarkly Setup:**

Flag key: `identity-verification-rollout`

Production targeting rules:
```
Wave 0: tenant_type = "test"  →  true
Wave 1: tenant_type = "live" AND arr_tier = "low" AND deployed_services CONTAINS "identity"  →  5% rollout
Wave 2: expand arr_tier filter, increment to 15%
Wave 3: expand arr_tier = "mid", increment to 35%
Wave 4: expand arr_tier to "mid-high", increment to 65%, extended bake
Wave 5: remove arr_tier filter, 100%
Rollback: set flag to false in production → immediate revert
```

---

## Framework Principles

- **PM owns intent.** The PM defines what changes, who is affected, impact, rollback path, earliest start, and target GA.
- **Decision support, not autopilot.** The skill identifies mandatory stages and suggests a cohort plan; the PM confirms and R&D validates technical feasibility.
- **Two ownership zones.** R&D owns R0–R1 (internal environments). PM owns R2–R4 (customer-facing exposure).

---

## Release Path Quick Reference

### LIGHTWEIGHT
- Triggers: Low impact + easy rollback + not NFI
- Rings: R0 → R4
- R3: Not required
- Notes: R&D validates internally; PM approves GA readiness.

### STANDARD
- Triggers: Medium impact OR coordinated rollback (and not NFI)
- Rings: R0 → R3 → R4
- R3: Test Tenants → targeted live cohorts
- Notes: PM owns full R3 plan.

### HIGH-TOUCH
- Triggers: NFI OR high impact OR irreversible rollback
- Rings: R0 → R1 → R2 → R3 → R4
- R3: Mandatory full cohort sequencing
- Notes: R2 entry requires NFI process completion. PM owns R2–R4.

---

## R3 Cohort Targeting Guidance

### Tenant types
- **Test Tenants:** Customer-purchased sandbox accounts. Always go first in R3.
- **Live Tenants:** Real production customer accounts. Sequenced by ARR and tenant count.

### Wave sizing
Default: 5% → 15% → 35% → 65% → 100% of eligible live tenants.
"Eligible" = tenants that meet the service and region criteria for this feature.

### Targeting dimensions
| Dimension | When to use |
|-----------|-------------|
| ARR tier | Default — use in all R3 plans |
| Tenant count cap | Use when you want to limit blast radius in early waves |
| Services | Use when the feature depends on specific deployed services |
| Region | Use only when regulatory or technical constraints require it; remove in later waves |

### Example — NFI, High Impact, 3-month GA
- Path: HIGH-TOUCH
- Rings: R0 → R1 → R2 → R3 → R4
- R3 cohort plan:
  - Wave 0: Test Tenants (48-hour bake)
  - Wave 1: 5% — Lowest ARR, no region filter, services filter applied
  - Wave 2: 15% — Low-mid ARR
  - Wave 3: 35% — Mid ARR
  - Wave 4: 65% — Mid-high ARR (extended bake — 1 week)
  - Wave 5: 100% — All eligible

---

## Gate Criteria (Suggested)

Between each wave, define observable go/no-go criteria:
- Error rate delta vs. baseline (suggest: < 0.1% increase)
- Latency regression (suggest: < 5% p99 increase)
- Support ticket spike (suggest: < 3 related tickets per 1,000 tenants)
- Bake time (suggest: 24–48 h for early waves, 1 week for high-ARR waves)

---

## LaunchDarkly Validation Checklist

Before committing to R3, confirm with R&D:

- [ ] Tenant type distinction: internal vs. external, Test vs. live
- [ ] Tenant count controls available
- [ ] ARR-based targeting and percentage waves supported
- [ ] Service-based targeting (per-tenant service list available)
- [ ] Region filtering supported
- [ ] Single feature toggle spanning all affected components
- [ ] Cursor–LaunchDarkly integration: read/write permissions, tenant attribute access, rollback via flag

---

## Industry Benchmarks

| Company | Practice | Idira Takeaway |
|---------|----------|----------------|
| Microsoft | Progressive exposure rings; pause on quality signal degradation | Set clear go/no-go gates between stages |
| Google SRE | Canary vs. stable baseline comparison; bake time before expansion | Define success metrics and bake time in the PRD |
| Palo Alto Networks (Cortex XDR) | Internal → Early Adopters → 10% → 45% → 100% | Use existing PANW examples; validate fit for Idira |
| Palo Alto Networks (Prisma Access) | Staged upgrade rings by users/groups/OS; location-based upgrades | Leverage same patterns for tenant-based rings |

*Examples are product-specific and do not establish a PANW-wide rollout policy.*
