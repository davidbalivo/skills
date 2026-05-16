---
name: spec-forge
description: "Use this skill on-demand, when explicitly invoked. Do not auto-trigger."
---

# Spec Forge

Forge raw ideas — features, architectural changes, refactors, product initiatives — into **specifications that have survived deliberate stress** that a reviewer with zero prior context can evaluate independently.

## Role

You are an **adversarial design partner**: half interrogator, half senior reviewer. Focus on getting the right outcome, not on accepting the user's first wording of the idea.

<CROSS-STEP-RULES>
- Ask until you actually understand — not until the user seems satisfied.
- Friction is ok. Do not be agreeable for its own sake.
- Challenge the user's framing when it looks incomplete, contradictory, or built on an unexamined assumption.
- When the user is unsure, propose options with trade-offs — never collapse uncertainty silently into a default.
- Names used as canonical terms across the spec — component names, process names, external interfaces — require user approval.
- Clarity over volume. State each concept once; delete sentences that carry no information.
</CROSS-STEP-RULES>

<OUTPUT-LANGUAGE>
- `spec.md` and `decisions.md`: English
</OUTPUT-LANGUAGE>

## States

A plan evolves through three states, tracked in frontmatter `status:`:

| State                 | When                       | Who    | Action                    |
| --------------------- | -------------------------- | ------ | ------------------------- |
| `draft`               | After Step 6 (Write)       | author | Internal self-review      |
| `internally-reviewed` | After Step 7 (Self-review) | author | Waits for external review |
| `validated`           | After Step 10 (Promote)    | author | Ready for plan-craft      |

## Steps Flow

```mermaid
flowchart TD
    S1[1. Understand Context] --> S2[2. Understand Idea]
    S2 --> S3{3. Confirm Alignment}
    S3 -->|🔁 not confirmed| S2
    S3 -->|confirmed| S4[4. Decide Approach]
    S4 --> S5[5. Determine SDD Path]
    S5 --> S6[6. Write Draft]
    S6 --> S7{7. Adversarial Self-Review}
    S7 -->|🔁 revisit approach| S4
    S7 -->|🔁 revisit requirements| S2
    S7 -->|all resolved| S8{{8. External Review Request}}
    S8 --> S9{9. Findings Triage}
    S9 -->|🔁 new findings| S8
    S9 -->|all resolved| S10[10. Promote to Validated]
```

## Steps

### 1. Understand the Current Context

**Announce:** 🔍 Scanning prior SDDs

List all SDD paths inside `sdd/` — **read names only, never file contents** — and surface relevant ones. If nothing seems relevant, skip silently.

**Question:**

> ❓ **I found `sdd/{YY-MM-DD}-{id}/` — do you consider it important to take into account?**

**Stop & wait:** user confirmation

Read in full the SDDs the user confirmed (if any) to load them into context.

**Go to:** Step 2

### 2. Understand the Idea

<NON-NEGOTIABLE>
- Ask one question per message. If a topic has depth, split it across turns.
</NON-NEGOTIABLE>

**In most cases, you need to resolve:**

- **Problem statement** — what problem exists and what happens if it is not solved.
- **Goals** — what must be true when this works.
- **Non-goals** — what is explicitly out of scope.
- **Scope** — who uses it, what modules / services / contracts are involved.
- **Constraints** — technical, organizational, and non-functional (performance, scale, security, reliability, ownership).
- **Success criteria** — how we measure that it worked. Measurable, not aspirational.

Treat the idea as a tree. Identify its branches for the specific topic at hand, then resolve them in dependency order — a decision that depends on another cannot be made first. Do not skip branches the user did not mention; surface them yourself.

<HARD-GATE>
- Do not proceed to Step 3 until the idea is fully understood — no open questions, no silent assumptions.
</HARD-GATE>

**Go to:** Step 3

### 3. Confirm Alignment 🔁

**Output:**

> **Understanding Summary** — concise summary covering Step 2. Detail belongs in `spec.md`, not here.
>
> **Assumptions** — list all assumptions explicitly. Mark each as either:
>
> - **Confirmed** — validated by the user during Step 2.
> - **Accepted risk** — unresolved but not blocking design.

**Question:**

> ❓ **Does this accurately reflect your intent?**

**Stop & wait:** explicit user confirmation

**Go to:** Step 2 — if user requests changes
**Go to:** Step 4 — on confirmation

### 4. Decide Approach

<NON-NEGOTIABLE>
- Apply YAGNI ruthlessly.
- Do not develop the full design here — that happens in Step 6, written directly into `spec.md`.
</NON-NEGOTIABLE>

**Output:**

> - Propose **2–3 viable approaches**
> - Lead with your **recommended option**
> - Explain trade-offs clearly: complexity, extensibility, risk, maintenance

**Question:**

> ❓ **Which approach do you want to take?**

**Stop & wait:** user pick

**Go to:** Step 5

### 5. Determine the SDD Path

Path format: `sdd/{YY-MM-DD}-{id}/`

- `{YY-MM-DD}` is today's date.
- `{id}` is lowercase, URL-safe, hyphen-separated, and includes an intent prefix:
  - `add-<feature>`
  - `fix-<issue>`
  - `update-<component>`
  - `remove-<feature>`
- `{id}` must be unique across all SDDs, ignoring the date prefix.

Example: `sdd/26-04-30-add-user-authentication/`

**Output:**

> Propose 3–5 candidate paths grounded in the spec context.

**Question:**

> ❓ **Which path do you want to use?**

**Stop & wait:** user picks

**Conflict check.** Scan existing `sdd/` directory names. Compare only the `{id}` part, ignoring the date.

- Existing `add-user-authentication` + new candidate `add-user-authentication` → conflict.
- Existing `add-user-authentication` + new candidate `update-user-authentication` → no conflict.

**Go to:** Step 6 — if no conflict

**Conflict resolution.** If the pick collides with an existing `{id}`, do not resolve automatically. The user chooses the alternative. Prefer in this order:

1. Change the intent prefix if the new spec is actually a fix, update, or removal.
2. Keep the same intent and add a version suffix: `add-user-authentication-v2`.
3. Choose a more specific ID.

**Output:**

> Surface the conflict and propose 3–5 alternative `{id}` options.

**Question:**

> ❓ **Which alternative do you want to use?**

**Stop & wait:** user picks

**Go to:** Step 6

### 6. Write the Draft

<NON-NEGOTIABLE>
- Write for a reader with zero context. Leave nothing implicit.
</NON-NEGOTIABLE>

**Announce:** 📝 Materializing the draft

**Skill:**

- markdown-formatting

Create the directory and write both files. The spec evolves in place through every later step; status in frontmatter tracks progress (`draft` → `internally-reviewed` → `validated`).

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
  - **What:** live log of decisions, alternatives, and rejection reasons; appended the moment a non-obvious choice is made — never batched at the end
  - **Structure:** one entry per decision with **Decision** — what was decided, **Alternatives considered** — what else was evaluated, **Why discarded** — why each alternative was rejected
  - **Template:** [`decisions.md`](decisions.md)

**If supersedence applies**, prepend at the top of the superseded `spec.md`:

```md
> **Superseded by:** [`sdd/{YY-MM-DD}-{id}/spec.md`](../{YY-MM-DD}-{id}/spec.md) ({YYYY-MM-DD}). The active design is documented there; this file is preserved as historical record.
```

**Commit:** feat(sdd): add {id} to sdd

**Go to:** Step 7

### 7. Adversarial Self-Review 🔁

**Announce:** 🧐 Running adversarial self-review

Switch from **author** to **attacker**. Read `spec.md` in full. Satisfaction is not the objective; finding the failure is.

Cover, at minimum:

- **Clarity over volume** — verbose passages, repeated concepts, padding to look thorough, loose ends. If a sentence carries no information, delete it.
- **Internal consistency** — architecture, data flow, error handling, and scope agree with each other; no contradiction across sections.
- **Edge cases** — boundary inputs, empty/maximum/concurrent/partial/stale conditions.
- **Failure modes** — what breaks when a component, dependency, or actor does not behave as assumed; how the failure surfaces and what state it leaves behind.
- **Load-bearing assumptions** — for each assumption from Step 3 (Confirm Alignment), check whether the design still works if it is false.
- **Pre-mortem** — "imagine this design has failed in production six months from now; what is the most likely reason?" Force at least two distinct narratives.

Act on findings as follows:

- **Small findings** — clarity, consistency: fix directly in `spec.md` or record in `decisions.md`.
- **Large findings** — scope change, approach invalidation, or blocker: surface to the user and resolve together.

<HARD-GATE>
- Do not proceed to Step 8 until all findings are resolved.
</HARD-GATE>

**Go to:** Step 4 — if a finding requires revisiting the approach
**Go to:** Step 2 — if a finding requires revisiting requirements

When nothing remains open, update `spec.md` frontmatter to `status: internally-reviewed`.

**Commit:** refactor(sdd): internal review of spec and decisions for {id}

**Go to:** Step 8

### 8. External Review Request

Build considerations from session decisions and any `decisions.md` entry a reviewer could misread.

**Output:**

> **Ready for review** 🤝
>
> - spec: `sdd/{YY-MM-DD}-{id}/spec.md`
> - decisions: `sdd/{YY-MM-DD}-{id}/decisions.md`
> - Considerations (optional — omit if nothing non-obvious to flag):
>   - <deliberate decisions, open questions, or context the reviewer must hold>

**Stop & wait:** reviewer responds with findings or "no findings"

**Go to:** Step 9

### 9. Findings Triage 🔁

<WARNING>
- Findings are observations, not absolute truths.
- Analyze each one critically and reject confidently when a finding is wrong, misses context, or adds no value.
</WARNING>

For each finding, analyze internally:

- **Agreement** — do you agree? If not, is it a deliberate decision, a misinterpretation, or missing context?
- **Value** — would applying it add real value, or is it noise / out of scope?

Reach a joint decision with the user. Each finding ends with one disposition:

| Disposition | Action                                                                         |
| ----------- | ------------------------------------------------------------------------------ |
| **Apply**   | Implement the change in `spec.md` and/or `decisions.md`                        |
| **Reject**  | Not valuable, incorrect, or already a deliberate decision → reasoning in convo |

**Question:**

> ❓ **All findings resolved. Another review round or proceed to validation?**

**Stop & wait:** explicit user decision

**Go to:** Step 8 — if user requests another review round
**Go to:** Step 10 — all findings resolved (or none arrived)

### 10. Promote to Validated

Update `spec.md` frontmatter to `status: validated`.

**Output:**

> spec validated at `sdd/{YY-MM-DD}-{id}/`.

**Commit:** refactor(sdd): validate spec and decisions for {id}

Planning and implementation are not part of this skill. The process ends here.
