---
name: sdd-impl-tdd
description: "Use this skill on-demand, when explicitly invoked. Do not auto-trigger."
---

# SDD Impl TDD

Execute a `validated` implementation plan through TDD per task and vertical slices per phase, each phase closed by its acceptance test.

## Role

You are an **execution engine**: drive plans to completion, never redesign them. Authority is limited to **TDD discipline and faithful execution** — not architecture, features, or scope.

<CROSS-STEP-RULES>
- Execute the plan; do not redesign it — no speculative features, no out-of-scope refactors, no silent decisions.
- Tests drive code; never the reverse.
- TDD Principles apply at every step — see ## TDD Principles.
</CROSS-STEP-RULES>

## Upstream Sources

- `sdd/{YY-MM-DD}-{id}/plan.md` — executable contract; `status: validated` required
- `sdd/{YY-MM-DD}-{id}/spec.md` and `decisions.md` — upstream rationale; read only when the plan does not add up

## TDD Principles

- **Tests verify behavior, not implementation.** A good test reads like a spec — exercises the
  public interface, survives internal refactors. See [.agents/skills/sdd-impl-tdd/tests.md](tests.md) and [.agents/skills/sdd-impl-tdd/mocking.md](mocking.md).
- **Vertical slices, never horizontal.** One test → one implementation → repeat.
- **Refactor only on GREEN.**
- **Tracer bullet first.** The thinnest end-to-end path that proves the task works.
- **Technical edge cases emerge here.** The plan defines business behavior; technical edge cases
  (null handling, boundary values, failure paths, etc.) are not in the plan — they are identified
  and scheduled in Stage 1 of each task.

## Plan State Awareness

The plan may be fresh or partially executed. Treat every `[x]` in `plan.md` as immutable history — do not re-verify, re-write, or re-commit completed items. Read the entire plan for context, then resume at the first unchecked item.

## Implementation Findings Protocol

When a discovery made **while coding** contradicts or extends the spec/plan, pause and register the finding via `sdd-spec-gap`. Show the diff and wait for explicit approval before continuing. This is the only exception to the no-interrupt rule within a phase.

**Skill:**

- sdd-spec-gap

Findings from external review do not go through this path.

## Steps

### 1. Input Validation

- Confirm `sdd/{YY-MM-DD}-{id}/plan.md` exists with `status: validated`
- Read all unchecked phases and tasks

<HARD-GATE>
- `plan.md` must exist with `status: validated` — if not, stop and ask.
</HARD-GATE>

**Go to:** Step 2

### 2. Setup

#### 2.1 Preconditions

Verify every unchecked item in `plan.md` › Setup › Preconditions. Each must resolve to a binary
✅. Preconditions are verified, never executed; no commits produced. If one fails, **stop and
ask** — implementation does not start until it is resolved out-of-band. Skip items already marked
[x].

- **Exit:** all items ✅. Mark each verified item [x] in `plan.md`.

#### 2.2 Groundwork

Execute every unchecked item in `plan.md` › Setup › Groundwork in order. Each must pass its
binary verification. Skip items already marked [x].

- **Exit:** all items verified. Mark each executed item [x] in `plan.md`, then group all
  groundwork into a single commit — the [x] marks ship inside it:

```
chore(sdd): groundwork for {id}
```

### 3. Per Phase

Execute phases in the order defined by `plan.md`. Each phase has two loops:

- **Outer loop (per phase):** the phase acceptance test wraps the slice. Written once at 3.1,
  closed once at 3.3.
- **Inner loop (per task):** every unchecked task in the phase runs through all stages of 3.2 —
  including its own self-review, review checkpoint, findings triage, and commits — before the
  next task starts.

Within a phase, tasks run sequentially. Do not start task N+1 until task N has exited 3.2 cleanly.

#### 3.1 Phase acceptance test (RED)

Write the phase's acceptance test as defined in `plan.md`. It must fail (RED) before any task
begins and stays RED until the phase closes. If the acceptance test already exists from a prior
session and is RED, keep it — do not rewrite.

This step runs **once per phase**, not per task.

#### 3.2 Per Task

For each unchecked task in the phase, run the eight stages below in order. Do not enter a stage
until the previous one has exited cleanly. Do not advance to the next task until Stage 8 of the
current task has exited.

**Exploratory tasks** (`[exploratory]` in `plan.md`): tests are throwaway. Honor the deletion
trigger declared in the task and remove the tests once the trigger fires.

##### Stage 1 — Plan cycles

- **Enter:** task starts. Record `hash-pre-task = HEAD` before writing any test or code.
- **Action:** derive the cycle sequence in two passes:
  1. From the task's **acceptance criteria** — one testable behavior per cycle. The first cycle
     is the task's tracer bullet.
  2. From **technical edge cases** for this boundary — append cycles for:
     - **Boundary values** — empty, min/max, single element
     - **Invalid inputs** — null/undefined, malformed or unexpected data
     - **Failure paths** — exceptions, rejected promises, IO errors
     - **State conflicts** — not found, duplicate, already exists
     - **Concurrency / idempotency** — duplicate requests, race conditions
     - **Authorization** — wrong owner, insufficient permissions
     - **Adversarial** — if you were trying to make this method fail, what would you try that
       isn't listed above? Reason from the interface, not from this list.

  Only append cycles that are testable against the public interface and add signal. Skip
  redundant or speculative cases.

- **Exit:** sequence announced.

##### Stage 2 — RED → GREEN

- **Enter:** Stage 1 exited.
- **Action:** for each cycle in order:
  - **RED**: write one failing test against the public interface.
  - **GREEN**: write the minimum code to pass — no anticipation of later cycles.
- **Exit:** every cycle is GREEN.

##### Stage 3 — Refactor

**Announce:** 🔧 "Refactoring"

- **Enter:** Stage 2 exited (all tests green).
- **Action:** evaluate, in order:
  1. **Duplication** — extract repeated logic; do not merge coincidental similarity.
  2. **Naming** — rename identifiers that survived GREEN under pressure but don't express intent.
  3. **Responsibility** — each unit does one thing; split if boundaries blur.
  4. **Dead paths** — remove unreachable branches or guards the type system already enforces.
  5. **Complexity** — simplify conditionals and nesting where the logic allows.

  Run all tests after each step. Show changes to the user.

- **Exit:** all tests still green; no further safe refactor identified.

##### Stage 4 — Validate

- **Enter:** Stage 3 exited.
- **Action:**
  1. Run `pnpm format` and `pnpm format:md` to apply formatting.
  2. Run `pnpm validate`. Loop until zero errors and zero warnings. If a fix changes observable
     behavior, return to Stage 2.
- **Exit:** validation is clean. If this is the **first pass** through Stage 4 for this task,
  commit:
  ```
  feat(<scope>): <task-id> initial implementation [Task <n>]
  ```
  On re-runs triggered by an `Apply` disposition, skip this commit.

##### Stage 5 — Adversarial self-review

**Announce:** 🔍 "Running adversarial self-review"

Switch from **implementer** to **auditor**, scoped to **this task only**. The goal is not to
confirm the task looks complete — it is to catch what slipped through the cycles before an
external reviewer finds it.

Run these probes against the task's diff:

- **Criterion coverage.** Every acceptance criterion of this task must map to at least one test.
  An untested criterion is a gap, not an assumption.
- **Technical edge case coverage.** Did the technical edge cases identified in Stage 1 become
  cycles? Are there obvious ones that slipped through?
- **Test honesty.** Each test that's GREEN — does it actually prove the behavior, or does it
  exercise the wrong path? A false positive is worse than a missing test.
- **Orphaned code.** Is every production change in this task driven by a test? Code written
  without a RED cycle is speculation.
- **Refactor debt.** Did anything persist that Stage 3 should have caught?
- **Interface drift.** Did any implementation decision silently change the public interface
  beyond what the plan specified? If yes, it is a finding.

If any probe is open, resolve it before advancing. Probes that imply a spec change go through the
Implementation Findings Protocol.

- **Exit:** all probes closed. If this is the **first pass** through Stage 5 for this task, mark
  this task and every completed subtask in `plan.md` with `[x]` (do not mark the phase — it closes
  at 3.3), then commit:
  ```
  feat(<scope>): <task-id> self-reviewed [Task <n>]
  ```
  On re-runs triggered by an `Apply` disposition, skip marking and skip this commit — the amendment
  commit fires at Stage 7 convergence instead.

##### Stage 6 — Ready for review

Output the following message, then **stop and wait** for the reviewer's response:

```markdown
**Ready for review**

- Implementation Plan: <relative path to plan.md>
- Phase: <phase id and title>
- Task: <task id and title>
- Cycles run: <count>
- Task diff: `git diff <hash-pre-task>..HEAD`
- Considerations:
  - <deliberate decisions, trade-offs, or context the reviewer must hold>
```

Then **stop and wait** for findings.

##### Stage 7 — Findings triage

**Findings are technical observations, not absolute truths. Aim for objective analysis, not blanket compliance.**

For each finding, analyze: do you agree? If not, is it a deliberate decision, a misinterpretation,
or missing context? Would applying it add real value, or is it noise / out of scope?

Reach a joint decision. Each finding ends with one disposition:

| Disposition | Action                                                                               |
| ----------- | ------------------------------------------------------------------------------------ |
| **Apply**   | Implement the change → return to Stage 2 for this task, then re-run Stages 3–6       |
| **Defer**   | Valid but out of scope → record via the `write-deferred-findings` skill              |
| **Reject**  | Not valuable, incorrect, or already a deliberate decision → reasoning stays in convo |

The `write-deferred-findings` skill must be invoked **only when the user confirms a Defer disposition**, and only at that moment. Do not load it preemptively.

**After processing all findings in this cycle:** if at least one finding was `Apply`, commit the
amendments. Use a counter `N` starting at 1, incremented per external review cycle that produced
changes:

```
refactor(<scope>): <task-id> review amendments r<N> [Task <n>]
```

If no finding was `Apply`, no commit is produced for this cycle.

**Then explicitly ask the user — do not advance silently:**

> "Findings de la ronda r<N> resueltos. ¿Quieres otra ronda de revisión (vuelve a Stage 6) o
> avanzo a la siguiente tarea (Stage 8)?"

Wait for the user's response. Only on explicit approval to advance does the task exit Stage 7.
If the user requests another pass, return to Stage 6 and repeat. The Stages 6–7 loop is the
default; advancing is the exception that requires explicit confirmation.

##### Stage 8 — Advance

When Stage 7 has converged, advance to the next unchecked task in the phase (return to Stage 1).
When no unchecked tasks remain, advance to 3.3.

#### 3.3 Phase gate

When every task in the phase has exited 3.2, the phase acceptance test must now be GREEN. If it
isn't, the phase slice is incomplete — identify the missing behavior, add it as a new task or
reopen the relevant one, and run it through 3.2.

All tasks in this phase are now [x] — no further marking needed for the phase itself (no commit —
the task commits already cover the work).

Present a single phase summary:

- Phase id and acceptance test name
- Tasks completed
- Total cycles and refactors applied across the phase

Then ask: **"Phase X complete. Acceptance test GREEN. Ready to continue?"**

User can:

- ✅ Approve → next phase (return to 3.1)
- 🔄 Request changes → reopen the relevant task and re-run 3.2 from Stage 1 or the appropriate
  stage
- 📝 Post-phase observation → triage as in Stage 7; **Apply** dispositions become work in the
  next phase, not retroactive edits to the closed one
- ⏸️ Stop

### 4. Final Gate

After the last phase's 3.3 has been approved, verify the cross-phase **Success Criteria** defined
in `spec.md`:

- Every Success Criterion is satisfied and observable
- Manual steps required for any criterion are completed by the user
- `plan.md` is fully marked done

If anything is unverified or unmarked, reopen the relevant phase. When clean, confirm the criteria
are satisfied and proceed to Step 5.

### 5. Implementation Closure

After the user approves the Final Gate, execute the closure sequence once:

1. Update `plan.md` frontmatter: `status: validated` → `status: implemented`.
2. Run `pnpm pending`.
3. Stage both files: `git add sdd/{id}/plan.md sdd/00-PENDING.md`.
4. Commit:
   ```
   chore(sdd): mark {id} as implemented
   ```

This step is not triggered automatically — execute it upon explicit user confirmation that the
implementation is complete.
