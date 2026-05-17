# Deferred Findings — Template

Reference for entries in `sdd/{spec-id}/deferred-findings.md`. Each finding follows the structure defined in [Finding Schema](SKILL.md#finding-schema).

---

## Example 1 — full body

## Repeated repository guard duplicated across handlers

<!-- finding
category: architecture
priority: 2
status: pending
date: 2026-05-17
spec: 042-billing-recovery
-->

- **Context:** Three handlers in `app/handlers/billing/` repeat the same repository-existence guard before delegating to the service layer. The guard predates the introduction of `RepositoryRegistry` in spec 038.
- **Risk:** Drift between handlers — if one updates the guard logic, the others silently retain the old check.
- **Impact:** Tenant onboarding bugs surface only in the handler that drifted; integration tests do not catch them because each handler is tested in isolation.
- **Future improvement:** Move the guard to `RepositoryRegistry.ensureForTenant()` and remove the per-handler implementations.

---

## Example 2 — minimal body

## Inconsistent date format in audit log entries

<!-- finding
category: tech-debt
priority: 3
status: pending
date: 2026-05-17
spec: 042-billing-recovery
-->

- **Context:** Audit log mixes ISO 8601 (`2026-05-17T10:23:00Z`) with locale-dependent strings (`5/17/2026 10:23 AM`) depending on the writing path. No active risk; affects readability when grepping logs.
