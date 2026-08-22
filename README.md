# skills

Private store of personal skills for agents (Claude Code and others), installable via [`npx skills`](https://github.com/vercel-labs/skills).

## Prerequisites

`npx skills` clones the repo with git, so you need GitHub auth configured:

- **SSH:** SSH key added to your GitHub account (`ssh -T git@github.com` should respond successfully), or
- **HTTPS:** [`gh auth login`](https://cli.github.com/) done.

## Installation

| Mode        | Command                                        | Destination                 | When to use                              |
| ----------- | ---------------------------------------------- | --------------------------- | ---------------------------------------- |
| Global      | `npx skills add davidbalivo/skills@<skill> -g` | `~/.claude/skills/`         | Transversal skills useful in any project |
| Per project | `npx skills add davidbalivo/skills@<skill>`    | `<project>/.claude/skills/` | Skills specific to the project's domain  |

## Updating

| Command                     | Effect                                   |
| --------------------------- | ---------------------------------------- |
| `npx skills update`         | Update all skills in the current project |
| `npx skills update -g`      | Update only global skills                |
| `npx skills update <skill>` | Update a single skill                    |

## Catalog

### Discovery

**[deep-alignment](skills/discovery/deep-alignment/)**
Structured interview to align the agent with the user before implementing

**[eli0](skills/discovery/eli0/)**
Create precise, visually led HTML explanations for intelligent readers with no prior context

**[eli5](skills/discovery/eli5/)**
Explain topics for readers with no prior context through HTML artifacts with big pictures and few words

### Engineering

**[audit-ts-code](skills/engineering/audit-ts-code/)**
Adversarial code quality auditor: reviews TypeScript changes across seven pillars before acceptance

**[git-operations](skills/engineering/git-operations/)**
Rules for local git: commit strategy, write-op confirmation, message format

**[md-essentials](skills/engineering/md-essentials/)**
Canonical Markdown formatting rules aligned to the project's Prettier configuration

**[sdd-audit-impl](skills/engineering/sdd-audit-impl/)**
External auditor for sdd-impl: stress-tests phases to break them before the phase gate accepts them

**[sdd-audit-impl-tdd](skills/engineering/sdd-audit-impl-tdd/)**
External auditor for sdd-impl-tdd: stress-tests TDD phases to break them before the phase gate accepts them

**[sdd-audit-plan](skills/engineering/sdd-audit-plan/)**
External auditor for sdd-plan: stress-tests plans to break them before sdd-impl does

**[sdd-audit-plan-tdd](skills/engineering/sdd-audit-plan-tdd/)**
External auditor for sdd-plan-tdd: stress-tests TDD plans to break them before sdd-impl-tdd does

**[sdd-audit-spec](skills/engineering/sdd-audit-spec/)**
External auditor for sdd-spec: stress-tests specs to break them before sdd-plan does

**[sdd-defer-finding](skills/engineering/sdd-defer-finding/)**
Formalizes out-of-scope findings into structured entries in `deferred-findings.md`

**[sdd-impl](skills/engineering/sdd-impl/)**
Executes a validated plan through vertical slices with per-task review checkpoints, without TDD protocol

**[sdd-impl-tdd](skills/engineering/sdd-impl-tdd/)**
Executes a validated plan through vertical-slice TDD with per-task review checkpoints

**[sdd-plan](skills/engineering/sdd-plan/)**
Converts a validated spec and decision log into a plan with testable criteria, without TDD protocol

**[sdd-plan-tdd](skills/engineering/sdd-plan-tdd/)**
Converts a validated spec and decision log into a concrete, TDD-ready plan

**[sdd-spec](skills/engineering/sdd-spec/)**
Interrogation flow that turns ideas into reviewable, zero-context specs

**[sdd-spec-gap](skills/engineering/sdd-spec-gap/)**
Detect, confirm, and trace gaps or ambiguities found during any SDD workflow step

**[step-based-skills-standard](skills/engineering/step-based-skills-standard/)**
Canonical structure, keywords, tags and mermaid conventions for step-based skills

**[ts-essentials](skills/engineering/ts-essentials/)**
Universal non-negotiable TypeScript rules: type safety, modeling, inference, nullability, async

### Language

**[spanish-artifacts](skills/language/spanish-artifacts/)**
Translate English or create and edit Spanish-language artifacts while preserving established English terminology

### Visual

**[sketchnote-slide](skills/visual/sketchnote-slide/)**
Generate sketchnote-style illustrations (1024×1024, transparent) for slides from a structured slide block

## Adding a new skill

1. Create folder `skills/<category>/<skill-name>/`. Prefer existing categories; if new, validate the name with a 3-5 option proposal.
2. Write `SKILL.md` with required YAML frontmatter (`name`, `description`).
3. Add an entry to the [catalog](#catalog) under the matching category, in alphabetical order.
4. Atomic commit: `feat(<category>): add <skill> skill`.

## Development

Markdown is auto-formatted with [Prettier](https://prettier.io/) (config in `.prettierrc`):

- VSCode: format on save (requires the `esbenp.prettier-vscode` extension).
- Claude Code: on Write/Edit via the `PostToolUse` hook in `.claude/settings.json`.
