---
name: grader/grade/opt-ins/penalty-patterns
description: >
  Register a new penalty pattern discovered during grading, apply it to
  the current group, and retroactively check all previously graded groups.
  Invoked by grader/grade when the professor chooses to register a new
  pattern at the end of a grading session.
---

# grader/grade/opt-ins/penalty-patterns

## Guiding Principles

**Never modify this skill file.** All output goes to `grading-analysis.md`
files and MIND.md.

**Verify before applying.** Never accept a professor-proposed penalty
without confirming the issue is present in the student's code.

**Retroactive consistency.** Once a penalty is registered, every
previously graded group must be checked.

---

## Context expected from grader/grade

When invoked, the following context is already available in the session:

- `<lab-slug>` and `<group-slug>` (the group just graded)
- The current `grading-analysis.md` for this group
- The Grading Procedure loaded from MIND.md
- Any unregistered deductions flagged by grader/grade

---

## Step 1 — Identify the new pattern

If grader/grade flagged a suspected pattern, present it for confirmation:

> ⚠️ Suspected new pattern: `<description>` (proposed: −N pts)
> Found in: `<file>:<lines>`
> Should I register this as a new penalty?

If the professor is proposing a pattern not yet in the analysis:
1. **Verify first — do not accept blindly.** Check whether the issue is
   actually present in the student's code.
2. If clearly confirmed: proceed to Step 2.
3. If evidence is ambiguous:
   > I checked `<file>:<lines>` and I'm not certain this applies because
   > `<what you observed>`. Could you clarify what specifically to look for?
4. If not present:
   > I looked at `<file>:<lines>` and did not find this issue — here is
   > what I see: `<snippet>`. Should I still apply the penalty?

Never apply a penalty without verification or explicit professor override.

---

## Step 2 — Register via grader/procedure

Once confirmed, invoke `grader/procedure` to add the penalty:
> ⚠️ New penalty pattern confirmed: `<description>` (proposed: −N pts)
> Invoking `grader/procedure` to register it.

`grader/procedure` will assign an ID (e.g. `IMPL-02`) and add it to the
relevant criterion's "Common deductions" table.

---

## Step 3 — Apply to current group

Update the current group's `grading-analysis.md`:
- Add a finding line: `> ❌ <penalty-ID>: <description>`
- Adjust the Score Decisions table for the affected criterion.
- Recompute the final grade.
- Update the group's row in the MIND.md scoring table.

---

## Step 4 — Retroactive check

Check every previously graded group for the same issue:
- Read their `grading-analysis.md`.
- If needed, read their code diff.

For each group where the issue is present:
- Add the finding line to their `grading-analysis.md`.
- Adjust their Score Decisions table and recompute their grade.
- Update their row in the MIND.md scoring table.

Report the result:

```
⚠️  New penalty registered: <ID> — <description> (−N pts)
    Applied to: <current-group-slug>
    Retroactive check:
      ❌ <group-slug> — issue found, score updated (X.X → X.X)
      ✅ <group-slug> — not affected
      ...
```

Wait for the professor to confirm before closing.
