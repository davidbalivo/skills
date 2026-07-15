---
status: draft
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
- **Internal services**: health, logs, metrics, dependencies.
- **Integration point**: incident tickets (Linear/Jira) with limited query language.

### Knowledge Artifacts

- Playbooks (predefined investigation flows for common problems).
- Investigation reports (auto-documented findings and diagnostics).
- Query patterns (reusable searches and data collection methods).

## Constraints

### Security

- Agent cannot access personally identifiable information (PII).
- Agent cannot access credentials or secrets.
- Agent cannot read sensitive databases or tables without explicit ACL.

### Operational

- Agent is read-only (no state changes, no remediation).
- Agent runs on-demand (triggered by incident tickets), not continuous monitoring.
- Investigation duration must complete within SLA (to be defined during planning).

### Technical

- Agent integrates via existing APIs (Kubernetes API, database connectors, service endpoints).
- Knowledge storage is in version-controlled repo (playbooks + investigation logs).
- No external LLM dependencies for playbook execution (keep it deterministic).

## Assumptions

- **Confirmed**: Incident entry point is via tickets (Linear/Jira).
- **Confirmed**: Agent learns by documenting investigation steps and findings.
- **Confirmed**: Hybrid approach: playbooks for common cases + agent exploration for edge cases.
- **Accepted risk**: Definition of "sensitive database" requires security review before implementation.

## Success Criteria

**Primary**: Mean time to diagnosis decreases by at least 30% for investigated incident types within 6 months.

**Secondary** (measured at 3 months):

- Agent resolves ≥50% of investigations without human refinement.
- ≥10 reusable playbooks created from real incidents.
- ≥30 team members have triggered at least one diagnostic run.
