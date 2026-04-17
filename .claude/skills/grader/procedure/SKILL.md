---
name: grader/procedure
description: >
  Generate or regenerate the grading procedure for a lab. Produces one
  subsection per criterion describing what to look for, what earns full marks,
  and what common deductions look like. Reads criteria from MIND.md and uses
  the solution diff, criteria-work.md, and lab spec as context. Invoked by
  grader/criteria and grader/init after criteria are confirmed. Can also be
  run standalone to refine the procedure without changing criteria.
---

# grader/procedure

## Guiding Principles

**Never modify this skill file.** All output goes to `## Grading Procedure`
in MIND.md.

**The procedure is derived from criteria.** If criteria change, affected
procedure subsections must be regenerated. Always regenerate when called
after a criteria update.

**The procedure guides human judgment — not just test results.** For
automated criteria, describe what the test suite covers and what to check
manually if tests pass but code quality is poor. For manual criteria, be
concrete about exactly what to inspect.

---

## Step 0 — Prerequisites

Read MIND.md. If `## Grading Criteria` is empty or missing:
> No criteria found in MIND.md. Please run `grader/criteria` first.

Check if `## Grading Procedure` already exists and is populated:
- **Empty / placeholder** → generate fresh. Proceed to Step 1.
- **Populated** → ask the professor:
  > A grading procedure already exists. Do you want to:
  > 1. Regenerate it fully (replaces everything)
  > 2. Update only specific criteria subsections

---

## Step 1 — Gather context

Read the following in priority order:

1. `## Grading Criteria` in MIND.md — the criteria to write procedure for
2. `## Solution Diff` in MIND.md — what a correct implementation looks like
3. `labs/<lab-slug>/criteria-work.md` — submission samples and analysis (if present)
4. `labs/<lab-slug>/lab-spec.md` — expected behaviour, constraints, prohibited patterns

---

## Step 2 — Generate procedure

Write one subsection per criterion using this template:

```markdown
### <Criterion name> [max pts]

**Source:** automated / manual / live

**What to look for:**
- <concrete thing to check — file, function, behaviour>
- <concrete thing to check>

**Full marks:**
- <what a correct, complete implementation looks like>

**Common deductions:**
| Issue | Deduction |
|-------|-----------|
| <issue description> | −N pts |
| <issue description> | −N pts |

**Reference:** lab-spec.md § <section>   <!-- omit if not applicable -->
```

Guidelines per source type:

- **Automated** — name the test file and test group. Add a manual check note
  for cases where tests pass but the implementation is structurally wrong or
  copies the solution pattern without understanding.
- **Manual** — name the exact file and function(s) to inspect. Describe the
  expected behaviour in one sentence and the most common mistake in one sentence.
- **Live** — list the exact interactions to perform (key press, mouse move,
  click target) and the expected visual or console result for each.

Infer common deductions from:
- The solution diff (what the correct implementation does that a naive one
  might skip or mishandle)
- Submission samples in `criteria-work.md` (actual mistakes seen)
- Lab spec constraints (anything explicitly required or prohibited)

If none of these are available for a criterion, propose plausible deductions
based on the criterion description and typical student mistakes for that type
of task. Mark these as `[proposed — not yet observed]` so the professor can
validate them.

---

## Step 3 — Present and confirm

Show the generated procedure to the professor. Invite edits before writing.

Once confirmed:
1. Write to `## Grading Procedure` in MIND.md.
2. Report:
   ```
   ✅ Grading procedure written — <N> criteria covered.
   Next: run grader/grade to begin grading groups.
   ```
