# Feature: <feature-name>

**⚠️ EXECUTION RULES — READ FIRST:**
- Implement ALL changes required by this plan
- Delete debug logs added during execution (keep pre-existing ones)
- Leave ALL changes UNSTAGED — do NOT run `git add` or `git commit`
- Only make code changes — no git operations

Validate documentation and codebase patterns before implementing. Match naming of existing utils, types, and models. Import from correct files.

---

## Feature Description

<Detailed description of the feature, its purpose, and user value>

## User Story

As a <user type>
I want to <action/goal>
So that <benefit/value>

## Problem Statement

<Define the specific problem or opportunity>

## Solution Statement

<Describe the proposed solution approach>

## Feature Metadata

**Feature Type**: [New Capability / Enhancement / Refactor / Bug Fix]
**Complexity**: [Low / Medium / High]
**Primary Systems Affected**: [Components/services]
**Dependencies**: [External libraries or services]
**Breaking Changes**: [Yes/No — explain if yes]

---

## CONTEXT REFERENCES

### Relevant Codebase Files — READ BEFORE IMPLEMENTING

- `path/to/file.py` (lines 15–45) — Why: Pattern for X to mirror
- `path/to/model.py` (lines 100–120) — Why: Database model structure

### New Files to Create

- `path/to/new_service.py` — Service implementation for X
- `tests/path/to/test_new_service.py` — Unit tests

### Relevant Documentation — READ BEFORE IMPLEMENTING

- [Doc Link](https://example.com/doc#section) — Why: Required for secure endpoints

### External API Research (if applicable)

**API**: [Name] v[Version]
**Research Doc**: `.agents/research/[api]-research.md`
**Documentation**: [Primary URL]

**Integration Pattern**:
```[language]
# Initialization, usage, error handling
```

**Critical Findings**: [Key findings impacting implementation]
**Validation Strategy**: [POC description, expected results, fallback]

### Patterns to Follow

**Naming Conventions**: [Examples from codebase]
**Error Handling**: [Examples from codebase]
**Logging Pattern**: [Examples from codebase]

---

## PARALLEL EXECUTION STRATEGY

### Dependency Graph

```
┌──────────────────────────────────────────┐
│ WAVE 1: Foundation (Parallel)            │
├──────────────────────────────────────────┤
│ Task 1.1: [Name]  │ Task 1.2: [Name]    │
│ Agent: [Role]     │ Agent: [Role]       │
└──────────────────────────────────────────┘
                    ↓
┌──────────────────────────────────────────┐
│ WAVE 2: Core (After Wave 1)              │
├──────────────────────────────────────────┤
│ Task 2.1: [Name] — Deps: 1.1, 1.2       │
└──────────────────────────────────────────┘
```

### Parallelization Summary

**Wave 1 — Fully Parallel**: Tasks [X, Y, Z] — no dependencies
**Wave 2 — Parallel after Wave 1**: Tasks [A, B]
**Wave 3 — Sequential**: Task [C] — needs Wave 2

### Interface Contracts

**Contract 1**: Task [X] provides [interface] → Task [A] consumes
**Mock for parallel work**: [How Task A can proceed while Task X is in progress]

### Synchronization Checkpoints

**After Wave 1**: `[integration test command]`
**After Wave 2**: `[integration test command]`

---

## IMPLEMENTATION PLAN

### Phase 1: Foundation & External Service Verification

**If using external APIs, Phase 1 MUST include:**
1. External service setup (accounts, API keys, config)
2. Verify services work as expected
3. Test integration patterns from research
4. Confirm feasibility before Phase 2

#### Task 1.1: External Service Setup & Verification (if applicable — BLOCKER)

**⚠️ BLOCKER**: If verification fails, update plan or escalate.

**Setup Steps**:
1. Review `.agents/research/[api]-research.md`
2. Create accounts/API keys for [services]
3. Configure dashboards (webhooks, OAuth, settings)
4. Store credentials in `.env` securely
5. Implement service initialization and connection validation

**Verification Tests**:
```bash
[commands to verify services]
```

**Checklist**:
- [ ] Accounts created and accessible
- [ ] API keys generated and stored securely
- [ ] Required features confirmed available
- [ ] Rate limits verified sufficient
- [ ] Authentication working
- [ ] Response format matches expectations
- [ ] Setup documented

**Expected Success**: [Exact output showing success]

**If Fails**: Document blocker, evaluate alternatives, update plan or STOP and escalate.

**DO NOT PROCEED UNTIL THIS PASSES**

#### Task 1.2: [ACTION] [target_file]

**Purpose**: [What this accomplishes]
**Dependencies**: Task 1.1 (if APIs), otherwise none
**Steps**: [Implementation steps]
**Validation**: `[command]`

### Phase 2: Core Implementation

[Main implementation — business logic, services, APIs, models]

### Phase 3: Integration

[Connect to routers, register components, update config, add middleware]

### Phase 4: Testing & Validation

[See TESTING STRATEGY below]

---

## STEP-BY-STEP TASKS

Tasks organized by execution wave. Same wave = safe to run in parallel.

**Task keywords**: CREATE · UPDATE · ADD · REMOVE · REFACTOR · MIRROR

---

### WAVE 1: Foundation

**If using external APIs, Task 1.1 MUST be service setup/verification.**

#### Task 1.1: External Service Setup (required if external APIs)

- **WAVE**: 1
- **AGENT_ROLE**: integration-specialist
- **DEPENDS_ON**: []
- **BLOCKS**: [All tasks using external services]
- **PROVIDES**: Verified service access, credentials, confirmed features
- **IMPLEMENT**: Create accounts, generate keys, configure dashboards, test connectivity
- **VALIDATE**: `[all verification tests]`
- **IF_FAILS**: Document blocker, evaluate alternatives, update plan or escalate

#### Task 1.2: {ACTION} {target}

- **WAVE**: 1
- **AGENT_ROLE**: [role]
- **DEPENDS_ON**: [1.1 if APIs, else none]
- **BLOCKS**: [IDs]
- **PROVIDES**: [Interface for downstream]
- **IMPLEMENT**: [Details]
- **PATTERN**: [Reference file:line]
- **VALIDATE**: `[command]`
- **INTEGRATION_TEST**: `[command with Wave 1 peers]`

**Wave 1 Checkpoint**: `[integration test after all Wave 1 tasks complete]`

---

### WAVE 2: Core (After Wave 1)

#### Task 2.1: {ACTION} {target}

- **WAVE**: 2
- **AGENT_ROLE**: [role]
- **DEPENDS_ON**: [Task 1.x]
- **BLOCKS**: [IDs]
- **PROVIDES**: [What it delivers]
- **USES_FROM_WAVE_1**: Task 1.1 provides [X], Task 1.2 provides [Y]
- **IMPLEMENT**: [Details]
- **VALIDATE**: `[command]`

**Wave 2 Checkpoint**: `[integration test]`

---

### WAVE 3: Integration (Sequential)

#### Task 3.1: {ACTION} {target}

- **WAVE**: 3
- **AGENT_ROLE**: integration-specialist
- **DEPENDS_ON**: [Task 2.x]
- **PROVIDES**: Final integrated feature
- **IMPLEMENT**: [Integration logic]
- **VALIDATE**: `[end-to-end test]`

**Final Checkpoint**: `[full feature test]`

---

## TESTING STRATEGY

**⚠️ ALL tests that can be automated MUST be automated.**

Manual testing is only acceptable when automation is physically impossible (hardware interaction, legal/compliance CAPTCHAs, bot-detection that defeats real browser automation). "Hard to automate" and "requires a browser" are NOT valid reasons — use Playwright.

| What you're testing | Tool |
|---|---|
| Backend logic, services, APIs | `pytest` (`tests/auto/`) |
| Frontend UI, user flows | Playwright (`frontend/tests/*.spec.ts`) |
| Full-stack integration | Playwright |
| Third-party dashboards | Playwright — browse actual site and assert state |
| Third-party REST APIs | `pytest` or Playwright `fetch()` |

For EACH test specify: Type · Automation Status (✅/⚠️) · Tool · File path · Run command · Manual justification (if ⚠️)

### Unit Tests

**Status**: ✅ Automated | **Tool**: [pytest/Jest] | **Location**: [path] | **Run**: `[command]`

### Integration Tests

**Status**: ✅ Automated | **Tool**: [pytest/Playwright] | **Location**: [path] | **Run**: `[command]`

### End-to-End Tests

**Status**: ✅ Automated (Playwright handles full E2E including auth flows)
**Tool**: Playwright | **Location**: `frontend/tests/[feature].spec.ts` | **Run**: `cd frontend && npx playwright test [feature]`

[Describe scenarios: login → action → assert result in UI and/or database]

### Third-Party Service Validation

**Status**: ✅ Automated via Playwright or direct API call

**Pattern** (Playwright):
```typescript
const res = await fetch('https://third-party.api/endpoint', {
  headers: { 'x-api-key': process.env.THIRD_PARTY_API_KEY! }
});
expect((await res.json()).someField).toBe(expectedValue);
```

**Pattern** (pytest):
```python
res = requests.get('https://third-party.api/endpoint',
    headers={'Authorization': f"Bearer {os.getenv('THIRD_PARTY_API_KEY')}"})
assert res.json()['someField'] == expected_value
```

### Manual Tests (only if automation is physically impossible)

Valid reasons: physical hardware (camera, mic, Bluetooth), legal/compliance action required by a human, strict bot-detection that real browser automation cannot bypass.

#### Manual Test [#]: [Name]

**Why Manual**: [Specific reason automation is impossible]
**Steps**: [1, 2, 3]
**Expected**: [Success criteria]

### Edge Cases

- **[Name]**: [Scenario] — ✅ `[pytest/Playwright command]`

### Test Automation Summary

| | Count | % |
|---|---|---|
| ✅ Backend (pytest) | [#] | |
| ✅ Frontend/E2E (Playwright) | [#] | |
| ✅ Third-party validation | [#] | |
| ⚠️ Manual | [#] | |
| **Total** | [#] | 100% |

**Goal**: 100% path coverage. Manual tests require explicit justification.

**Execution agent**: CREATE all automated test files as implementation tasks. RUN after each wave checkpoint.

---

## VALIDATION COMMANDS

### External-connection test policy (full-suite runs)

Full-suite runs (baseline + final validation) **exclude tests that open live external
connections** (broker / IB / market-data / live network services) **by default** — on shared
or multi-worktree machines they open real connections that can disrupt a live process (e.g. a
running trading orchestrator) and may hang. The execute skill deselects them unless this plan
opts in here.

- **Run external-connection tests during validation?** ☐ No (default) ☐ Yes
- **Deselect command (default-skip):** `[e.g. pytest tests/ -m "not ib and not external"  OR  pytest tests/ --ignore=tests/test_ib_realtime.py]`
- **If Yes — exact paths/markers + safe command:** `[e.g. pytest -m ib]` and confirm **no live external process (e.g. trading orchestrator) is running** during the run.

### Level 0: External Service Validation (if applicable — MUST PASS FIRST)

```bash
[verify credentials, connectivity, features, rate limits]
```

**If failed**: Check credentials, network, service status. Upgrade plan, redesign, or STOP.
**DO NOT PROCEED UNTIL ALL SERVICES PASS**

### Level 1: Syntax & Style

[Linting/formatting commands]

### Level 2: Unit Tests

[Unit test commands]

### Level 3: Integration Tests

[Integration test commands]

### Level 4: E2E / Manual Validation

[E2E test commands and any manual steps]

---

## ACCEPTANCE CRITERIA

- [ ] External services set up, verified, and documented (if applicable)
- [ ] All specified functionality implemented
- [ ] All validation commands pass (including Level 0)
- [ ] Unit test coverage 80%+
- [ ] Integration and E2E tests pass
- [ ] Code follows project conventions
- [ ] No regressions
- [ ] Documentation updated (if applicable)
- [ ] Performance and security addressed (if applicable)

---

## COMPLETION CHECKLIST

- [ ] External service verification passed (if applicable)
- [ ] All tasks completed in wave order
- [ ] Each task validation passed
- [ ] All validation levels executed (0–4)
- [ ] All automated tests created and passing
- [ ] Manual tests documented with instructions
- [ ] Full test suite passes (unit + integration + E2E)
- [ ] No linting/type errors
- [ ] All acceptance criteria met
- [ ] Code reviewed for quality
- [ ] **⚠️ Debug logs added during execution REMOVED (keep pre-existing)**
- [ ] **⚠️ CRITICAL: Changes UNSTAGED — NOT committed**

---

## NOTES

[Additional context, design decisions, trade-offs]
