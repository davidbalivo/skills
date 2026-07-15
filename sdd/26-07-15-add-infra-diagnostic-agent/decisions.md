---
status: internally-reviewed
---

# Decisions Log

## Approach Selection: Hybrid Playbook + Agent Exploration

**Decision**: Use a hybrid model where playbooks handle common incident types (80%) and the agent can explore beyond playbooks for novel cases (20%).

**Alternatives considered**:

- Pure agent-driven exploration: Flexible but unpredictable and hard to audit.
- Pure playbook-first: Safe and auditable but requires constant maintenance and can't handle novel cases.

**Why discarded**:

- Pure exploration: Risk of inefficient queries, non-deterministic behavior, and difficulty explaining agent decisions in incident reviews.
- Pure playbooks: Inflexible; real incidents don't always fit predefined categories, and the system can't learn beyond existing playbooks.

**Trade-off accepted**: Requires maintaining two investigation paths (playbooks + agent logic). Benefit: pragmatic MVP with controlled exploration that learns incrementally.

---

## Internal Services Scope Deferred to Planning

**Decision**: "Internal services" scope is not enumerated in the spec. Instead, agent will query only allowlisted services defined in configuration during planning.

**Why**: Service topology is context-specific and evolves. Hardcoding a list in spec would be brittle. Allowlist approach is flexible and auditable.

**Risk accepted**: Scope ambiguity until planning phase. Mitigated by explicit "hard dependency for implementation."

---

## Investigation Documentation Stored in Git Repo

**Decision**: Auto-generated investigation reports are markdown files stored in a version-controlled repo, keyed by incident ticket ID.

**Alternatives considered**:

- Database-backed storage: More queryable but adds infrastructure, harder to audit/review.
- Slack/Linear comments: Ephemeral, not searchable over time.

**Why git repo**: Cheap, auditable (git history), version-controlled, integrates cleanly with playbook versioning.

**Implication**: Investigation data is semi-public (anyone with repo access can read). If sensitive findings arise, separate repo or access controls required.

---

## Security Enforcement Mechanism Deferred to Planning

**Decision**: Spec defines security _constraints_ (no PII, no credentials, no sensitive DB access) but defers _mechanisms_ (IAM policies, code boundaries, data masking) to planning.

**Why**: Mechanism depends on deployment architecture (which services, which cloud, existing IAM model). Can't be universal in spec.

**Risk accepted**: Security is load-bearing. Implementation must include detailed threat model and enforcement verification. Flagged as hard dependency.

---

## Success Criteria Revised: Actionability Over Adoption Count

**Decision**: Primary metric is "actionable diagnostics" (≥50% lead to resolution or root cause), not volume or adoption alone.

**Why**: A diagnostic that 100 people run but causes false escalations is worse than one 10 people run but trust. Actionability is the real measure of reducing MTTR.

**Trade-off**: Requires manual review/feedback to measure. Adoption metrics are secondary signals.

---
