# Gradual Rollout Plan — Reference

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
