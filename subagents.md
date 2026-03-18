# Add Subagent Definitions to ai-dev-env

## Background

The `execute` skill invokes three post-execution skills as **parallel subagents** via the `Agent` tool
after every execution run completes:

1. `execution-report` — documents what was implemented vs. planned
2. `acceptance-criteria-validate` — verifies each acceptance criterion was met
3. `code-review` — technical review of all changed files

The `execute` skill's instructions say:

> "launch the following skills as **parallel subagents** using the Agent tool. Start all available
> skills simultaneously — do NOT wait for one to finish before starting the others."

**The problem:** The `Agent` tool requires a registered **agent definition** (an `.md` file in an
`agents/` directory). These three are only defined as skills (`skills/*/SKILL.md`). When the execute
skill tries to invoke them via `Agent`, it gets:

> `Agent type 'ai-dev-env:execution-report' not found.`

As a fallback, the executing agent used `superpowers:code-reviewer` (a real agent definition from
the superpowers plugin) instead of `ai-dev-env:code-review`. The other two didn't run at all.

---

## What Needs to Be Done

Create an `agents/` directory in the plugin root and add three agent definition files — one per skill.

### Directory structure to add

```
ai-dev-env/
├── agents/                              ← new directory
│   ├── execution-report.md              ← new file
│   ├── acceptance-criteria-validate.md  ← new file
│   └── code-review.md                   ← new file
├── skills/
│   └── ...                              (unchanged)
└── .claude-plugin/
    └── plugin.json                      (unchanged — no registration needed for agents/)
```

### Agent definition format

Each file follows the same format as other Claude Code agent definitions (e.g., superpowers'
`code-reviewer.md`):

```markdown
---
name: <agent-name>
description: |
  <copy the description from the corresponding SKILL.md frontmatter, verbatim>
model: inherit
---

<brief system prompt explaining what this agent does>

When invoked, use the Skill tool to run the `ai-dev-env:<agent-name>` skill with any context
you were provided in your prompt. Follow the skill's instructions exactly and return its output.
```

The agent body intentionally stays thin — all the real logic lives in the skill. The agent wrapper
exists only to make the skill invocable via the `Agent` tool.

---

## The Three Files to Create

### `agents/execution-report.md`

- `name`: `execution-report`
- `description`: copy from `skills/execution-report/SKILL.md` frontmatter (line 3)
  > `Use when generating an implementation report after feature completion documenting what was done, divergences from plan, and test results`
- Body: invoke `ai-dev-env:execution-report` skill with the provided context

### `agents/acceptance-criteria-validate.md`

- `name`: `acceptance-criteria-validate`
- `description`: copy from `skills/acceptance-criteria-validate/SKILL.md` frontmatter (line 3–4)
  > `Use when an agent executing an implementation plan claims to have finished, to validate that all acceptance criteria were actually met. Locates acceptance criteria from the plan file, acceptance_criteria.md, or the request itself, then investigates the codebase and surfaces a pass/fail verdict per criterion.`
- Body: invoke `ai-dev-env:acceptance-criteria-validate` skill with the provided context

### `agents/code-review.md`

- `name`: `code-review`
- `description`: copy from `skills/code-review/SKILL.md` frontmatter (line 3)
  > `Use when performing a technical pre-commit code review on recently changed files for bugs, security issues, and standards compliance`
- Body: invoke `ai-dev-env:code-review` skill with the provided context

---

## How the Agent Tool Resolves Names

When the execute skill calls `Agent(subagent_type="ai-dev-env:execution-report")`, Claude Code
looks for an agent definition named `execution-report` within the `ai-dev-env` plugin's `agents/`
directory. The `agents/` directory is discovered automatically — no changes to `plugin.json` are
needed.

---

## Deployment Steps (after implementing)

1. Implement the three agent files in the repo
2. Bump the version in `.claude-plugin/plugin.json` (e.g., `1.0.0` → `1.1.0`)
3. Commit and push
4. Update the local plugin installation:
   ```bash
   claude plugin update ai-dev-env
   ```
   or uninstall and reinstall if the update command isn't available:
   ```bash
   claude plugin remove ai-dev-env
   claude plugin install <repo-url-or-path>
   ```
5. Verify the agents are registered by running a test execution and confirming all three post-execution
   subagents fire without `Agent type not found` errors

---

## Verification

After updating the plugin, run `/ai-dev-env:execute` on any plan. The Output Report section should
be followed by three parallel subagent invocations that all complete successfully — no fallback to
`superpowers:code-reviewer` and no silent skips.
