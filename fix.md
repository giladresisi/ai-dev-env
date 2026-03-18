# Fix: Post-execution subagent issues in `ai-dev-env:execute` skill

**Date:** 2026-03-18

## What Happened

After Phase 2 execution, the skill's post-execution subagents ran incorrectly:
- Only 2 of 3 subagents launched (the 3rd — `ai-dev-env:code-review` — was missed)
- The wrong `subagent_type` was used for one agent (`superpowers:code-reviewer` instead of `general-purpose`)
- The Output Report was declared before subagents completed, so a REJECTED verdict could never gate completion

## Root Causes

1. The executor stopped reading after launching 2 agents — missed the 3rd (`ai-dev-env:code-review`).
2. `superpowers:code-reviewer` was used as `subagent_type` instead of `general-purpose` — this bypasses actual skill invocation.
3. Subagents ran in background after the Output Report was written, removing any ability for a REJECTED verdict to block completion.

## Rules for Post-execution Subagents

- **All 3 are mandatory** — none can be skipped.
- **Always use `subagent_type: "general-purpose"`**; the prompt must begin with `"Use the Skill tool to invoke ai-dev-env:<skill-name>"` so the skill is actually invoked inside the agent.
- **Launch all 3 foreground** (not background) before writing the Output Report so that failures can gate completion.
