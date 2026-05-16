# Step-Based Skill Standard — Examples

Demonstrates all structural elements defined in `SKILL.md`. Examples are adapted from real
SDD-flow skills.

---

## Skill Skeleton

```markdown
---
name: example-skill
description: "On demand, when invoked by user."
---

# Example Skill

Short description of what the skill produces.

## Role

Short statement of identity and stance the skill adopts; shapes tone and decision bias.

<CROSS-STEP-RULES>
- Ask until you actually understand — not until the user seems satisfied.
- Challenge the user's framing when it looks incomplete or contradictory.
</CROSS-STEP-RULES>

<OUTPUT-LANGUAGE>
- `spec.md` — English
- `decisions.md` — English
</OUTPUT-LANGUAGE>

## States

| State                 | When                       | Who    | Action                 |
| --------------------- | -------------------------- | ------ | ---------------------- |
| `draft`               | After Step 3 (Write)       | author | Internal self-review   |
| `internally-reviewed` | After Step 4 (Self-review) | author | Awaits external review |
| `validated`           | After Step 6 (Triage)      | author | Ready for next phase   |

## Steps Flow

` ` `mermaid
flowchart TD
S1["1. Input Validation"]
S2["2. Setup"]
S3{{"3. External Review"}}
S4["4. Findings Triage 🔁"]
S5["5. Promote"]

    S1 --> S2
    S2 --> S3
    S3 --> S4
    S4 -->|"🔁 another round"| S3
    S4 -->|all resolved| S5

` ` `

## Steps
```

---

## Step Examples

### 1 — Conditional navigation, no user interaction

Based on sdd-spec Step 1 (SDD scan).

```markdown
### 1. Understand the Current Context

Scan `sdd/` — directory names only, never file contents. Identify paths that share topic or scope
with the current request. If nothing seems relevant, proceed silently.

**Go to:** Step 2 — if nothing relevant found
**Go to:** Step 2 — after user confirms which SDDs to consider
```

---

### 2 — HARD-GATE + Question + Stop & wait

Based on sdd-spec Step 3 (Understanding Lock).

```markdown
### 3. Understanding Lock

Present an understanding summary and a list of all assumptions, each marked as `Confirmed` or
`Accepted risk`. If any assumption is unresolved and blocks the design, return to Step 2.

<HARD-GATE>
- Do not proceed to Step 4 without explicit user confirmation of the summary.
</HARD-GATE>

**Question:**

> ❓ **Does this accurately reflect your intent?**
>
> - Confirm to proceed
> - Correct anything before we move to design

**Stop & wait:** explicit user confirmation

**Go to:** Step 2 — if any blocking assumption is unresolved
**Go to:** Step 4 — on confirmation
```

---

### 3 — Announce + Output + Stop & wait + 🤝 handoff

Based on sdd-spec Step 8 (External Review Request).

```markdown
### 8. External Review Request

Derive considerations from decisions made this session and from `decisions.md` that a reviewer
might misread without context.

**Announce:** 🧐 Requesting external review

**Output:**

> **Ready for review** 🤝
>
> - spec: `sdd/{YY-MM-DD}-{id}/spec.md`
> - decisions: `sdd/{YY-MM-DD}-{id}/decisions.md`
> - Considerations:
>   - <deliberate decisions or context the reviewer must hold>

**Stop & wait:** reviewer responds with findings or "no findings"

**Go to:** Step 9
```

---

### 4 — Loop: 🔁 + Go to (back) + Commit + Skill

Based on sdd-impl-tdd Stages 6–7 (Task Review → Findings Triage loop).

```markdown
### 5. Task Review

Emit the handoff message and wait for reviewer findings.

**Output:**

> **Ready for review** 🤝
>
> - Implementation Plan: `<path to plan.md>`
> - Phase: `<id and title>`
> - Task: `<id and title>`
> - Cycles run: `<count>`
> - Task diff: `git diff <hash>..HEAD`
> - Considerations:
>   - <decisions, trade-offs, context>

**Stop & wait:** reviewer findings

**Go to:** Step 6

---

### 6. Findings Triage 🔁

Findings are technical observations, not absolute truths. Analyze each critically. For each
finding reach a joint decision with the user — Apply, Defer, or Reject.

If at least one finding was Apply, commit amendments using counter N (1-indexed, incremented per
review cycle that produced changes).

**Commit:** `refactor(<scope>): <task-id> review amendments r<N> [Task <n>]`

**Skill:**

- write-deferred-findings — only when user confirms Defer disposition

**Question:**

> ❓ **Findings r\<N\> resolved. Another review round or advance to next task?**
>
> - Another round → return to Step 5
> - Advance → proceed to Step 7

**Stop & wait:** explicit user approval to advance

**Go to:** Step 5 — if user requests another round
**Go to:** Step 7 — if user approves advance
```

---

### 5 — 💬 Status (inline update during execution)

`💬 Status` is not a step keyword — it is emitted during execution as a progress signal. It does not
appear in the step definition.

Example of what the agent emits during execution:

> **💬 Status:** Migrating Step 3 of 9 — Understanding Lock

The step definition itself contains no 💬 reference:

```markdown
### 4. Migrate All Steps

Read each step in the original skill and rewrite it using the new format. If a step cannot be
expressed in the new format, surface it to the user before continuing.

**Announce:** 🔧 Migrating skill to standard

**Commit:** `refactor(sdd): migrate example-skill to skill standard`

**Go to:** Step 5
```

---

### 6 — Announce + Artifacts + NON-NEGOTIABLE + Commit

```markdown
### 6. Write the Draft

Create the directory and write both files.

<NON-NEGOTIABLE>
- Write for a reader with zero context. Leave nothing implicit.
- Self-contained from the first write — no "TBD" sections.
</NON-NEGOTIABLE>

**Announce:** 📝 Materializing the draft

**Artifacts:**
```

sdd/
└── {YY-MM-DD}-{id}/
├── spec.md
└── decisions.md

```

- **spec** — `sdd/{YY-MM-DD}-{id}/spec.md`
  - **What:** self-contained specification; evolves in place through every later step
  - **Structure:** frontmatter (`status: draft`); Problem statement, Goals, Non-goals, Scope, Constraints, Assumptions, Success criteria
  - **Template:** [`spec.md`](spec.md)

- **decisions log** — `sdd/{YY-MM-DD}-{id}/decisions.md`
  - **What:** live log of decisions, alternatives, and rejection reasons; appended the moment a non-obvious choice is made
  - **Structure:** one entry per decision with Decision / Alternatives considered / Why discarded
  - **Template:** [`decisions.md`](decisions.md)

**Commit:** `feat(sdd): add {id} to sdd`

**Go to:** Step 7
```
