# spec-gap-register

## Installation

Per project:

```bash
npx skills add davidbalivo/skills@spec-gap-register
```

Global:

```bash
npx skills add davidbalivo/skills@spec-gap-register -g
```

## About

Structured three-step flow (detect → decide → register) for surfacing and tracing gaps, ambiguities, or unresolved constraints discovered during any SDD workflow step. Keeps `spec.md` and `decisions.md` consistent via a `## Change Traceability` section.

## When to use it

Invoke when any SDD skill (e.g. `plan-craft`) surfaces a gap, ambiguity, or constraint not covered by the current spec or decisions.

## Invocation

```
/spec-gap-register
```
