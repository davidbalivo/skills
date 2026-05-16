# plan-craft

## Installation

Per project:

```bash
npx skills add davidbalivo/skills@plan-craft
```

Global:

```bash
npx skills add davidbalivo/skills@plan-craft -g
```

## About

Converts a validated SDD spec and decisions log into a concrete, TDD-ready implementation plan a zero-context engineer can execute independently.

## When to use it

Invoke after `spec-forge` has produced a validated spec. Requires `sdd/{YY-MM-DD}-{id}/spec.md` and `sdd/{YY-MM-DD}-{id}/decisions.md` to exist and be complete.

## Invocation

```
/plan-craft
```
