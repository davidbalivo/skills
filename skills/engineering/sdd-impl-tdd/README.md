# sdd-impl-tdd

## Installation

Per project:

```bash
npx skills add davidbalivo/skills@sdd-impl-tdd
```

Global:

```bash
npx skills add davidbalivo/skills@sdd-impl-tdd -g
```

## About

Executes a validated SDD implementation plan through vertical-slice TDD: drives each phase via its outer acceptance test, delivers tasks via RED → GREEN → REFACTOR cycles, keeps `plan.md` in sync, and commits by logical unit.

## When to use

Invoke after `sdd-plan-tdd` has produced a validated plan. Requires `sdd/{YY-MM-DD}-{id}/plan.md` with `status: validated`.
