# sdd-spec-gap

## Installation

Per project:

```bash
npx skills add davidbalivo/skills@sdd-spec-gap
```

Global:

```bash
npx skills add davidbalivo/skills@sdd-spec-gap -g
```

## About

Structured three-step flow (detect → decide → register) for surfacing and tracing gaps, ambiguities, or unresolved constraints discovered during any SDD workflow step. Keeps `spec.md` and `decisions.md` consistent via a `## Change Traceability` section.

## When to use

Invoke when any SDD skill (e.g. `sdd-plan`) surfaces a gap, ambiguity, or constraint not covered by the current spec or decisions.
