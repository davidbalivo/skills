# sdd-plan

## Installation

Per project:

```bash
npx skills add davidbalivo/skills@sdd-plan
```

Global:

```bash
npx skills add davidbalivo/skills@sdd-plan -g
```

## About

Variant of [`sdd-plan-tdd`](../sdd-plan-tdd/) without the TDD protocol.

**Drops:**

- RED → GREEN → REFACTOR cycles
- Outer/inner loop framing
- Handoff to `sdd-impl-tdd`

**Keeps:** testable acceptance criteria and the same phase/task structure.

Converts a validated SDD spec and decisions log into a concrete implementation plan a zero-context engineer can execute independently.

## When to use it

Invoke after `sdd-spec` has produced a validated spec. Requires `sdd/{YY-MM-DD}-{id}/spec.md` and `sdd/{YY-MM-DD}-{id}/decisions.md` to exist and be complete. Prefer this over `sdd-plan-tdd` when TDD ceremony is not required.

## Invocation

```
/sdd-plan
```
