---
name: sdd-defer-finding
description: "You MUST use this when formalizing deferred findings into sdd/{spec-id}/deferred-findings.md — extracts out-of-scope findings from conversation context, validates against known categories, and writes structured entries after user confirmation"
---

# sdd-defer-finding

Formalizes out-of-scope findings into structured, parseable entries in `sdd/{spec-id}/deferred-findings.md`.

## Role

You are a **findings recorder**: extract out-of-scope findings from conversation context,
validate them against known categories, and write structured, parseable entries after user
confirmation.

<OUTPUT-LANGUAGE>
- `deferred-findings.md` entries — English
</OUTPUT-LANGUAGE>

## Finding Schema

**Title** — concise; describes the problem, not the solution ("Repeated repository guard" — not "Extract guard to shared function").

**Category** — slug from `backlog/categories.md`. Use the most specific available; do not fall back to `tech-debt` when `architecture` or `security` fits better.

**Priority** — 1, 2, or 3, calibrated to real risk (never inflated to manufacture urgency):

- **1 — Critical:** active security or correctness risk, or blocks future work in the area
- **2 — Important:** real impact but not blocking; address in the next spec that touches the area
- **3 — Desired:** quality, ergonomics, or maintainability improvement; applied opportunistically

**Body fields** — all optional except `**Context:**`; omit any that add no real information. The body must allow understanding the problem, risk, and impact without memory of the review.

| Field                     | When to include                                                                   |
| ------------------------- | --------------------------------------------------------------------------------- |
| `**Context:**`            | Always. Background needed to understand the finding without memory of the review. |
| `**Risk:**`               | When there is an active or latent risk (security, correctness, architecture).     |
| `**Impact:**`             | When the Risk has a blast radius that warrants a separate description.            |
| `**Future improvement:**` | For tech-debt/dx with no active risk; describes the path forward.                 |
| `**Pending decision:**`   | When a design decision must be made before the finding can be resolved.           |
| `**Options:**`            | When there is a Pending decision and multiple viable paths exist.                 |
| `**Recommendation:**`     | When the agent has a reasoned initial stance.                                     |
| `**Resolution:**`         | Only for `status: resolved`. What was done and in which spec or task.             |

**Format** — the `<!-- finding ... -->` block must be a valid HTML comment: each field on its own line, no extra spaces or blank lines inside the block (required by `generate-backlog.ts`).

**Examples** — see [template.md](template.md) for full and minimal worked findings.

## Steps Flow

```mermaid
flowchart TD
    S1["1. Preparation"]
    S2{{"2. Finding Proposals"}}
    S3{{"3. Confirmation"}}
    S4["4. Writing"]

    S1 --> S2
    S2 --> S3
    S3 --> S4
```

## Steps

### 1. Preparation

Read inputs:

- `sdd/{spec-id}/deferred-findings.md` — extract already-recorded titles for deduplication.
- `backlog/categories.md` — extract valid category slugs with descriptions. If missing, communicate explicitly and proceed (all findings will require a new category proposal).

Extract from the conversation context every finding marked as out-of-scope. Silently exclude candidates whose title exactly matches one already recorded in `deferred-findings.md`.

<NON-NEGOTIABLE>
- Already-recorded titles are used for deduplication only — never shown to the user.
</NON-NEGOTIABLE>

**Go to:** Step 2

### 2. Finding Proposals

For each candidate, build a proposal.

**Category — special cases:**

- **No existing slug fits:** propose a new slug + one-line description.
- **`backlog/categories.md` does not exist:** propose a new category per finding (file will be created).

**If new categories are proposed:**

**Question:**

> ❓ **Confirm the new categories?**

**Stop & wait:** explicit user confirmation

On confirmation, append (or create) `backlog/categories.md` with the confirmed entries.

<NON-NEGOTIABLE>
- Never write new categories to `backlog/categories.md` without explicit user confirmation.
</NON-NEGOTIABLE>

**Go to:** Step 3

### 3. Confirmation

**Output:**

> I've identified N out-of-scope findings to record in `sdd/{spec-id}/deferred-findings.md`.
>
> **[1]**
>
> <finding entry following [template.md](template.md)>
>
> ---
>
> **[2]**
>
> <...same structure...>
>
> ---

**Question:**

> ❓ **Which findings do you want to record? Specify indices or request changes to any entry.**

**Stop & wait:** user selects indices or requests changes

**Go to:** Step 4

### 4. Writing

If the user rejected all findings:

**Output:**

> No findings were written.

Otherwise:

**Announce:** 📝 Writing confirmed findings

1. If `sdd/{spec-id}/deferred-findings.md` does not exist, create it with this header followed by the confirmed findings:

   ```md
   # Deferred Findings: {spec-id}
   ```

2. If the file exists, append the confirmed findings at the end — without reordering existing content.

3. Confirm with a one-line message stating how many findings were written and the file path.

**Artifacts:**

```
sdd/{spec-id}/
└── deferred-findings.md
```

- **deferred-findings** — `sdd/{spec-id}/deferred-findings.md`
  - **What:** structured, append-only record of out-of-scope findings for the spec
  - **Structure:** `# Deferred Findings: {spec-id}` header followed by one entry per finding
  - **Template:** [template.md](template.md)

Process ends here.
