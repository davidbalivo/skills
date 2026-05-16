---
status: draft
---

# Implementation Plan: [Feature Name]

## Overview

[One sentence]

## Architectural Invariants

[List durable decisions]

## Setup

### Preconditions

Facts about the environment outside the repo. Verified, not executed. The plan does not start until all are ✅.

- [ ] [Statement: what must be true]
  - Verification: ✅ / ❌ — [binary check]
  - Resolution path: [how to fix if ❌ — handled out-of-band]

### Groundwork

Non-behavioral work inside the repo executed before Phase 1. Produces commits. No acceptance criteria, only binary verification.

1. [ ] [Action]
   - Files: `path/to/file`
   - Verification: [binary check — command runs, file contains key, import resolves]
   - Decision: [decisions reference]

## Phase 1: Walking Skeleton

[Description]

**Acceptance test (outer loop):** [name and one-line behavior the phase must satisfy end-to-end]

### Phase 1 Tasks

1. [ ] Task name
   - Boundary: `module: <name>; public interface: <signature or endpoint>`
   - Acceptance:
     1. [tracer] `<input> → <output>` _or_ Given/When/Then
     2. `<input> → <output>` _or_ Given/When/Then
   - Decision: [decisions reference]
   - Subtasks:
     - [ ] Subtask 1

## Phase N: [Phase Name]

[Description]

**Acceptance test (outer loop):** [name and one-line behavior]

### Phase N Tasks

1. [ ] Task name
   - Boundary: `module: <name>; public interface: <signature or endpoint>`
   - Acceptance:
     1. [tracer] `<input> → <output>` _or_ Given/When/Then
     2. `<input> → <output>` _or_ Given/When/Then
   - Decision: [decisions reference]
   - Subtasks:
     - [ ] Subtask 1
