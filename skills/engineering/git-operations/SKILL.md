---
name: git-operations
description: You MUST use this skill before any git operation — defines commit strategy, constraints, and message format for all local git work.
---

# Git Operations

This skill defines the rules for all git operations in the project: what to commit together, how to format messages, and what requires user confirmation.

**Announce at start:** 📣 "I'm using the git-operations skill"

## Commit Strategy

- **Group by feature**: files that contribute to a single logical change commit together
- **Isolate unrelated changes**: never mix different features or fixes — split into separate atomic commits
- **Scope**: use the specific module or feature name (e.g., `feat(api)`, `fix(ui)`)

## Constraints

- **Local only**: no `push`, `pull`, or `fetch` — remote sync is user-only
- **Strict confirmation**: explain and get explicit "OK" before any write operation (commit, reset, merge)
- **Auto-read**: `status`, `diff`, and `log` require no confirmation
- **Ask when unsure**: never assume or guess on missing information — ask before acting

## Message Format

`type(scope): description` — header only, no body, no footer. Present tense, imperative mood, <72 chars, lowercase first character of `description`, no trailing period.

| Type       | Purpose                        |
| ---------- | ------------------------------ |
| `feat`     | New feature                    |
| `fix`      | Bug fix                        |
| `docs`     | Documentation only             |
| `style`    | Formatting/style (no logic)    |
| `refactor` | Code refactor (no feature/fix) |
| `perf`     | Performance improvement        |
| `test`     | Add/update tests               |
| `build`    | Build system/dependencies      |
| `ci`       | CI/config changes              |
| `chore`    | Maintenance/misc               |
| `skill`    | Skill add/update/remove        |

## Skill Artifacts

Changes inside a skill folder use `skill(<skill-name>)`.

## Examples

- `feat(db): add mongodb schema for transactions`
- `fix(clerk): resolve session timeout bug`
- `skill(git-operations): refine commit strategy`

## Breaking Changes

```
# Exclamation mark after type/scope
feat!: remove deprecated endpoint
```
