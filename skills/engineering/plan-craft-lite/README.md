# plan-craft-lite

## Installation

Per project:

```bash
npx skills add davidbalivo/skills@plan-craft-lite
```

Global:

```bash
npx skills add davidbalivo/skills@plan-craft-lite -g
```

## About

Lighter variant of [`plan-craft`](../plan-craft/): drops the TDD protocol (RED → GREEN → REFACTOR cycles, outer/inner loop framing, handoff to `tdd-exec`) while keeping testable acceptance criteria and the same phase/task structure.

Converts a validated SDD spec and decisions log into a concrete implementation plan a zero-context engineer can execute independently.

## When to use it

Invoke after `spec-forge` has produced a validated spec. Requires `sdd/{YY-MM-DD}-{id}/spec.md` and `sdd/{YY-MM-DD}-{id}/decisions.md` to exist and be complete. Prefer this over `plan-craft` when TDD ceremony is not required.

## Invocation

```
/plan-craft-lite
```
