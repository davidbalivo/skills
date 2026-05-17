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

| Skill                                                                        | Category      | Mode        | Description                                                                                         |
| ---------------------------------------------------------------------------- | ------------- | ----------- | --------------------------------------------------------------------------------------------------- |
| [deep-alignment](skills/discovery/deep-alignment/)                           | `discovery`   | Global      | Structured interview to align the agent with the user before implementing                           |
| [git-operations](skills/engineering/git-operations/)                         | `engineering` | Per project | Rules for local git: commit strategy, write-op confirmation, message format                         |
| [sdd-spec](skills/engineering/sdd-spec/)                                     | `engineering` | Per project | On-demand interrogation flow that turns ideas into reviewable, zero-context specs                   |
| [sdd-plan](skills/engineering/sdd-plan/)                                     | `engineering` | Per project | Converts a validated spec and decision log into a plan with testable criteria, without TDD protocol |
| [sdd-plan-tdd](skills/engineering/sdd-plan-tdd/)                             | `engineering` | Per project | Converts a validated spec and decision log into a concrete, TDD-ready plan                          |
| [sdd-impl-tdd](skills/engineering/sdd-impl-tdd/)                             | `engineering` | Per project | Executes a validated plan through vertical-slice TDD with per-task review checkpoints               |
| [sdd-spec-gap](skills/engineering/sdd-spec-gap/)                             | `engineering` | Per project | Detect, confirm, and trace gaps or ambiguities found during any SDD workflow step                   |
| [defer-finding](skills/engineering/defer-finding/)                           | `engineering` | Per project | Formalizes out-of-scope findings into structured entries in `deferred-findings.md`                  |
| [md-essentials](skills/engineering/md-essentials/)                           | `engineering` | Global      | Canonical Markdown formatting rules aligned to the project's Prettier configuration                 |
| [step-based-skills-standard](skills/engineering/step-based-skills-standard/) | `engineering` | Per project | Canonical structure, keywords, tags and mermaid conventions for step-based skills                   |

## Adding a new skill

1. Create folder `skills/<category>/<skill-name>/`. Prefer existing categories; if new, validate the name with a 3-5 option proposal.
2. Write `SKILL.md` with required YAML frontmatter (`name`, `description`).
3. Write `README.md` following the per-skill contract defined in [CLAUDE.md](./CLAUDE.md).
4. Add a row to the [catalog](#catalog).
5. Atomic commit: `feat(<category>): add <skill> skill`.

## Development

Markdown is auto-formatted with [Prettier](https://prettier.io/) (config in `.prettierrc`):

- VSCode: format on save (requires the `esbenp.prettier-vscode` extension).
- Claude Code: on Write/Edit via the `PostToolUse` hook in `.claude/settings.json`.
