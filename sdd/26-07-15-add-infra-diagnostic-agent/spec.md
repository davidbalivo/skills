---
status: internally-reviewed
---

# Infrastructure Diagnostic Agent

## Problem Statement

Infrastructure knowledge is centralized in a small group of people. When incidents occur, diagnostics are slow because knowledge is scattered and not codified. This creates a bus factor risk: if key people are unavailable, operational effectiveness drops.

## Goals

- Automatically diagnose infrastructure incidents (Kubernetes, databases, internal services) without human intervention.
- Accumulate operational knowledge over time through documented investigations.
- Reduce mean time to diagnosis for infrastructure problems.
- Enable team members without deep infrastructure knowledge to access diagnostics.

## Non-Goals

- Execute remediations or state-changing actions.
- Replace on-call procedures or escalation.
- Build a general-purpose LLM application.
- Handle application-level diagnostics (only infrastructure).

## Scope

### Users

- On-call engineers receiving incident reports.
- Team members investigating infrastructure issues.
- System: an autonomous agent triggered by incident tickets.

### Systems Covered

- **Kubernetes**: pod state, events, logs, resource usage.
- **Databases**: replication status, query performance, resource constraints.
- **Internal services**: subset to be scoped during planning. Agent can only query services explicitly allowlisted in configuration.
- **Integration point**: incident tickets (Linear/Jira) with limited query language.

### Knowledge Artifacts

- **Playbooks**: predefined investigation flows for common problem types (stored in repo).
- **Investigation reports**: auto-generated markdown documents capturing investigation steps, queries run, and findings. Stored in repo with incident ticket reference.
- **Query patterns**: reusable searches and data collection methods extracted from successful investigations.

## Constraints

### Security

- Agent cannot access personally identifiable information (PII).
- Agent cannot access credentials or secrets.
- Agent cannot read sensitive databases or tables without explicit ACL.
- Security enforcement mechanism (IAM policies, data masking, code boundaries) to be detailed during planning. Hard dependency for implementation.

### Operational

- Agent is read-only (no state changes, no remediation).
- Agent runs on-demand (triggered by incident tickets), not continuous monitoring.
- Investigation must complete within a bounded time (baseline SLA to be defined during planning, e.g., 5–15 minutes for typical incidents).

### Technical

- Agent integrates via existing APIs (Kubernetes API, database connectors, service endpoints).
- Knowledge storage is in version-controlled repo (playbooks + investigation logs).
- Playbook execution is deterministic (no LLM-based branching for core investigation flows). Agent logic outside playbooks may use generative models for exploration (to be scoped during planning).

## Assumptions

- **Confirmed**: Incident entry point is via tickets (Linear/Jira).
- **Confirmed**: Agent learns by documenting investigation steps and findings.
- **Confirmed**: Hybrid approach: playbooks for common cases + agent exploration for edge cases.
- **Accepted risk**: Definition of "sensitive database" requires security review before implementation.

## Success Criteria

**Primary**: Mean time to diagnosis decreases by at least 30% for investigated incident types (baseline measured at launch, review at 6 months).

**Secondary** (measured at 3 months):

- Agent-generated diagnostics are actionable (lead to resolution or identified root cause) in ≥50% of investigations.
- ≥10 reusable playbooks created from real incidents.
- ≥50% of on-call team has used the agent at least once.

**Failure indicators**:

- Diagnostics consistently miss the root cause (false negatives).
- Playbooks become stale (>30% fail to run due to infrastructure changes).
- Agent accesses data it should not (security breach).
