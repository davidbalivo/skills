# sdd-impl

## Installation

Per project:

```bash
npx skills add davidbalivo/skills@sdd-impl
```

Global:

```bash
npx skills add davidbalivo/skills@sdd-impl -g
```

## About

Variant of [`sdd-impl-tdd`](../sdd-impl-tdd/) without the TDD protocol.

**Drops:**

- RED → GREEN → REFACTOR cycles
- Phase acceptance test as RED outer-loop driver
- Separate Refactor step

**Keeps:** plan-driven execution, vertical-slice phases, phase acceptance test as verification gate, per-task test cases including technical edge cases, self-review, findings triage, and the same commit pattern.

Executes a validated SDD implementation plan through vertical slices and per-task review checkpoints, keeping `plan.md` in sync and committing by logical unit.

## When to use

Invoke after `sdd-plan` has produced a validated plan. Requires `sdd/{YY-MM-DD}-{id}/plan.md` with `status: validated`. Prefer this over `sdd-impl-tdd` when TDD ceremony is not required.
