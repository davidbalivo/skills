# CLAUDE.md

Repo-specific instructions. User-level instructions in `~/.claude/CLAUDE.md` apply automatically and are not duplicated here.

## Purpose

Private store of personal skills consumed via [`npx skills`](https://github.com/vercel-labs/skills) (vercel-labs). Skills are installable globally (`-g`) into `~/.claude/skills/` or per-project into `<project>/.claude/skills/`.

## Response Style

Be extremly concise and sacrifice grammar for the sake of concision

## Structure

```
skills/<category>/<skill-name>/
  SKILL.md    # agent-facing instructions (required)
  README.md   # human-facing docs (required)
```

Prefer existing categories. New category names require a 3-5 option proposal per the user-level naming rule.

## SKILL.md contract

YAML frontmatter required, with both fields as strings:

```yaml
---
name: <skill-name>
description: <one-line description used by the agent to decide when to invoke>
---
```

Body of the file = instructions the agent will follow when the skill is activated.

## README.md contract (per skill)

Human-facing. Sections in this order:

1. Title
2. **Installation** — per project first, global second; labels (`Per project:` / `Global:`) as plain text outside the bash blocks.
3. About — 1-2 line description.
4. When to use.

## Before committing a skill

- Frontmatter parses as valid YAML with both `name` and `description` as strings.
- `npx skills add davidbalivo/skills@<skill-name>` would discover the skill (folder reachable, `SKILL.md` present).
- Top-level [README.md](./README.md) catalog updated with a new row.
