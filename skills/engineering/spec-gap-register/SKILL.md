---
name: spec-gap-register
description: "Use this skill on-demand, when explicitly invoked. Do not auto-trigger. For gaps, ambiguities, or constraints not covered by spec or decisions."
---

# Spec Gap Register

Use when planning or implementation reveals **gaps, ambiguities, or constraints not covered** in the original spec or decisions.

## Role

You are a **spec steward**. Your authority is limited to surfacing, confirming, and tracing changes — not deciding them. Every resolution requires explicit user approval before it is written.

<CROSS-STEP-RULES>
- Never register a finding without user confirmation.
- Integrate changes into normative sections of `spec.md` first, then register the finding in `## Change Traceability`. Never add findings inline inside normative sections.
- One finding per invocation. If multiple gaps exist, surface them one at a time.
</CROSS-STEP-RULES>

<OUTPUT-LANGUAGE>
- Traceability entries (`spec.md`, `decisions.md`) — English
</OUTPUT-LANGUAGE>

## Steps Flow

```mermaid
flowchart TD
    S1{"1. Detect"}
    S2{{"2. Decide"}}
    S3["3. Register"]
    END(["End"])

    S1 -->|confirmed| S2
    S1 -->|rejected| END
    S2 --> S3
```

## Steps

### 1. Detect

Describe the gap, ambiguity, or constraint to the user:

- **What was found:** precise description of the issue
- **Where it surfaced:** which step or artifact triggered it
- **Why it matters:** impact on implementation if left unresolved

**Stop & wait:** user confirms the finding is real and worth resolving

**Go to:** Step 2 — if confirmed
**Go to:** End — if rejected

### 2. Decide

**Output:**

> - Which sections of `spec.md` are affected (modified or created)
> - What the spec or decisions will say after the change

**Question:**

> ❓ **Does this resolution look correct?**

**Stop & wait:** explicit user decision on resolution

**Go to:** Step 3

### 3. Register

Apply the resolution and record traceability in both files.

**Update `spec.md`**

Integrate the resolved requirement directly into the relevant normative section. Then append to `## Change Traceability` (create section if absent, as the last section):

```markdown
## Change Traceability

### [FINDING] YYYY-MM-DD - Brief title

- **Sections affected:** `Existing Section` / new section `New Section Name`
- **Summary:** What was found and why it wasn't covered
- **Resolution:** How the spec now handles it
```

**Update `decisions.md`**

Append to `## Change Traceability` (create section if absent, as the last section):

```markdown
## Change Traceability

### [FINDING] YYYY-MM-DD - Brief title

- **What was found:** Description of the gap or ambiguity
- **Why it matters:** Impact on implementation
- **Decision:** What was decided
```

**Output:**

> Finding registered:
>
> - `spec.md` — section(s) updated + traceability entry added
> - `decisions.md` — traceability entry added
