# Fix: Post-Execution Subagents Not Running

## Problem

The three post-execution subagents (`execution-report`, `acceptance-criteria-validate`,
`code-review`) are defined at the end of `skills/execute/SKILL.md` under
**"Invoke Post-Execution Skills"**, but they consistently do not run.

### Root Cause

The Output Report section — which appears *before* "Invoke Post-Execution Skills" in the
skill — ends with this block:

```
✅ **EXECUTION COMPLETE** — All implementations, tests, and validations passed.
**Ready for commit:** Yes - all changes verified and production-ready.
```

This is a hard terminal signal. Once the model writes "EXECUTION COMPLETE", it treats the
task as resolved and stops generating. The post-execution section that follows is never
reached — not because the model decides to skip it, but because it never reads that far
after declaring completion.

This is a structural ordering problem: the Output Report signals done *before* the
subagents are invoked.

---

## Proposed Fix

Move the subagent invocation to **before** the Output Report, and make the Output Report
depend on their results.

### Changes to `skills/execute/SKILL.md`

#### 1. Replace the "Output Report" section header and its Final Status block

**Current** (end of Output Report):
```markdown
### Final Status

✅ **EXECUTION COMPLETE** - All implementations, tests, and validations passed successfully.

**Ready for commit:** Yes - all changes verified and production-ready.

---

## Invoke Post-Execution Skills

After generating the Output Report above, launch the following skills as **parallel
subagents** using the Agent tool...
```

**Replace with:**
```markdown
---

## Step 5: Invoke Post-Execution Subagents

**Run this step BEFORE generating the Output Report.**

Spawn all available post-execution skills as parallel subagents using the Agent tool.
Start all simultaneously in a single message — do NOT wait for one before starting others.
The Output Report cannot be written until their results are in hand.

[... same subagent definitions as currently in "Invoke Post-Execution Skills" ...]

Wait for all subagents to complete, then incorporate their findings into the Output Report
sections below.

---

## Output Report

**ONLY generate this report after:**
- Passing the Verification Gate, AND
- Receiving results from all post-execution subagents above.

[... existing Output Report content ...]

### Code Review
[Paste verdict and key findings from the code-review subagent]

### Acceptance Criteria Validation
[Paste ACCEPTED / REJECTED / NEEDS REVIEW verdict and per-criterion results]

### Execution Report Summary
[Paste coverage gaps and key findings from the execution-report subagent]

### Final Status

✅ **EXECUTION COMPLETE**
```

#### 2. Remove the standalone "Invoke Post-Execution Skills" section

Delete the section currently at the bottom of the skill. Its content moves into the new
**Step 5** above the Output Report.

---

## Why This Works

The model cannot write "EXECUTION COMPLETE" until it has subagent results to fill the
required Output Report subsections. The incompleteness is self-enforcing: an Output Report
with `[Paste verdict...]` placeholders still present is visibly unfinished.

Contrast with the current structure where subagents are requested *after* the report is
already complete — at that point the model has no incentive to continue.

---

## Affected File

`skills/execute/SKILL.md` — sequential execution section and team-based section both have
the same issue and need the same structural fix applied to each.
