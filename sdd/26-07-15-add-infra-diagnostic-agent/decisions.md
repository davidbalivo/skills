---
status: draft
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
