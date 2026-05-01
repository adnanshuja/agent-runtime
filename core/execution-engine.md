# Execution Engine — Authoritative runtime controller. Single entry point. No bypass.

This file is the **sole entry point** for all agent behavior. Every module (`context/`, `modes/`, `core/pipeline.md`, `core/gates.md`, `enforcement/`, `trace/`) is subordinate to this controller.

ALL tasks MUST follow this pipeline. DO NOT bypass any stage. DO NOT proceed past a failing gate. DO NOT accept output without a complete trace.

---

## 1. SYSTEM ROLE

This file is the **authoritative execution layer**. It defines:

- The complete execution flow (pipeline orchestration)
- Integration points for all subordinate modules
- Hard validation gates that can restart or reject execution
- The structured output contract

The agent is a runtime executing this file. Every task — regardless of perceived simplicity — enters through this engine. No task starts without classification. No change lands without diff discipline. No output is accepted without a trace.

Violation of any stage gate or enforcement rule triggers the rejection protocol (`enforcement/rejection-protocol.md`). Two violations of the same rule triggers automatic FAIL.

---

## 2. EXECUTION PIPELINE

The pipeline runs in strict sequence. Each stage gates the next. No stage may be skipped unless the active mode explicitly permits it (see `modes/`).

### Stage 1: Task Intake

Receive and normalize the raw request.

```
INTAKE: [raw request]
NORMALIZED: [one-sentence task description]
CONSTRAINTS: [explicit boundaries extracted]
FILES: [known or suspected affected files]
```

FAIL if:
- Request is ambiguous (what does "fix" mean? what does "improve" mean?)
- Success criteria cannot be extracted
- Affected files cannot be identified

DO NOT proceed to classification without a normalized task.

### Stage 2: Context Classification

Delegates to `context/classifier.md`.

Determine:
- **Task type**: BUG FIX | FEATURE | REFACTOR | EXPLORATION
- **Risk level**: LOW | MED | HIGH
- **Risk heuristics**: Apply per `context/heuristics.md` (can only RAISE risk)
- **Behavioral mapping**: Per `context/behavioral-matrix.md`

```
CLASSIFICATION:
  TYPE: [BUG FIX | FEATURE | REFACTOR | EXPLORATION]
  RISK: [LOW | MED | HIGH]
  HEURISTICS: [applied heuristics, if any]
  BEHAVIORAL_MAP: [reference to matrix entry]
```

FAIL if classification is skipped. FAIL if heuristics were not applied. FAIL if BUG FIX or REFACTOR lacks a type signature in the request.

### Stage 3: Mode Selection

Maps classification to an execution mode per `modes/mode-effects.md`.

| Risk | Type | Default Mode |
|------|------|-------------|
| LOW | any | FAST |
| MED | BUG FIX / FEATURE | STRICT |
| MED | REFACTOR | STRICT |
| HIGH | any | STRICT |
| any | any with intermittent behavior | DEBUG |

Override only on explicit user instruction ("switch to FAST mode"). The agent MUST NOT infer permission to downgrade.

```
MODE: [STRICT | FAST | DEBUG]
MODE_FILE: [modes/strict.md | modes/fast.md | modes/debug.md]
```

FAIL if mode is not explicitly stated. FAIL if mode contradicts classification (e.g., FAST on HIGH risk).

### Stage 4: Assumptions Extraction

Per `core/pipeline.md` S1.

Extract minimum 3 assumptions. Each must be:
- Verifiable (provably true or false)
- Actionable (if wrong, changes the approach)
- Explicitly sourced from the task, not invented

```
ASSUMPTIONS:
- [assumption 1] (source: [where this comes from])
- [assumption 2] (source: [where this comes from])
- [assumption 3] (source: [where this comes from])
```

GATE H2: FAIL if fewer than 3. FAIL if any assumption is unverifiable without asking. FAIL if any assumption lacks a source.

DO NOT proceed to interpretations until assumptions pass H2.

### Stage 5: Multiple Interpretations

Per `core/pipeline.md` S2.

Generate 2+ interpretations of the task (3+ for HIGH risk or DEBUG mode). Each interpretation must include:
- Key difference vs alternatives
- Implication if correct
- Cost if wrong

Include at least one interpretation the agent considers unlikely (trap avoidance).

FAST mode minimum: 1 interpretation (S2 may be combined).

```
INTERPRETATIONS:
1. [interpretation A]
   DIFFERENCE: [one sentence]
   IF_CORRECT: [what happens]
   COST_IF_WRONG: [wasted effort or damage]
2. [interpretation B]
   DIFFERENCE: [one sentence]
   IF_CORRECT: [what happens]
   COST_IF_WRONG: [wasted effort or damage]
```

GATE H3: FAIL with single interpretation (STRICT/DEBUG). FAIL if any interpretation missing cost-if-wrong.

### Stage 6: Decision + Justification

Per `core/pipeline.md` S3.

Select one interpretation. Explicitly reject each alternative with reasoning.

State:
- Confidence: HIGH | MED | LOW
- What new information would change this decision

```
DECISION: [interpretation ID]
JUSTIFICATION: [reasoning]
REJECTED:
- [alt 1]: [why rejected]
- [alt 2]: [why rejected]
CONFIDENCE: [HIGH | MED | LOW]
WOULD_CHANGE_IF: [condition]
```

GATE H4: LOW confidence without user confirmation → STOP. FAIL if alternatives not explicitly rejected.

### Stage 7: Planning

Per `core/pipeline.md` S4.

Produce ordered steps. Each step must have:
- A specific action
- A testable verification criterion
- Estimated line count and affected files
- A backout step

```
PLAN:
1. [action] -> VERIFY: [testable outcome]  [~N lines, file]
2. [action] -> VERIFY: [testable outcome]  [~N lines, file]
BACKOUT: [recovery command]
TOTAL ESTIMATE: ~N lines across N files
```

GATE H5: FAIL if any step lacks a testable verification criterion. FAIL if no backout step. FAIL if plan would exceed diff cap (R6) without explicit justification.

DO NOT execute without an approved plan.

### Stage 8: Execution with Diff Discipline

Per `core/pipeline.md` S5, enforced by `enforcement/diff-discipline-engine.md` (R1-R8).

One step at a time. Verify after each step before proceeding.

Every hunk requires per-change justification:
- WHY: reason this change is needed
- RISK IF WRONG: what breaks
- ALTERNATIVE: what else was considered

```
STEP [N]: [action]
CHANGE:
  FILE: [path]
  HUNK: [+N/-N lines at line range]
  JUSTIFICATION:
    WHY: [reason]
    RISK_IF_WRONG: [what breaks]
    ALTERNATIVE: [what else was considered]
RESULT: [success | failure]
VERIFICATION: [evidence that step works]
```

FAIL on verification failure without stopping. FAIL on out-of-plan file. FAIL on speculative code (R2). FAIL on style drift (R3). FAIL on hidden dependencies (R7). FAIL on exceeding diff cap without justification (R6).

### Stage 9: Validation Gate

Per `core/pipeline.md` S6, hardened by the Validation Gate defined in Section 4 below.

Run tests + linters + diff discipline check (R1-R8). Confirm reproduction is fixed (BUG FIX). Check edge cases.

```
VERIFICATION:
  Tests: [pass | fail | skipped]
  Linters: [pass | fail | skipped]
  git diff --stat: [files changed, lines]
  R1-R8: [all pass | violations — list]
  Edge cases: [covered | gaps]
  Reproduction fixed: [yes | no | n/a]
```

If violations found → route to `enforcement/rejection-protocol.md` for classification (SOFT | HARD | CRITICAL) and resolution.

FAIL on test failure. FAIL on unrelated changes (R1). FAIL on skipped verification. FAIL on two violations of the same rule (Two-Strike → full rollback).

### Stage 10: Final Output

Per `core/pipeline.md` S7, contracted by `core/contracts.md` and `trace/output-contract.md`.

Produce:
1. Human-readable structured output (per Section 5)
2. Machine-readable execution trace JSON (per Section 6)
3. Scorecard evaluation (per `core/scorecard.md`)

FAIL at H8 if trace missing required fields. FAIL if scorecard weighted < 7 or any dimension below threshold.

---

## 3. DIFF DISCIPLINE INTEGRATION

The Diff Discipline Engine (`enforcement/diff-discipline-engine.md`) is embedded at Stage 8 and re-checked at Stage 9. All 8 rules are active unless the mode explicitly relaxes them.

| Rule | Description | STRICT | FAST | DEBUG |
|------|-------------|--------|------|-------|
| R1 | Scope — every line traces to task | ENFORCED | ENFORCED | ENFORCED |
| R2 | No speculation — no dead code | ENFORCED | ENFORCED | ENFORCED |
| R3 | Style fidelity — match existing conventions | ENFORCED | ENFORCED | ENFORCED |
| R4 | No injection — no LLM instructions in code | ENFORCED | ENFORCED | ENFORCED |
| R5 | Verification first — tests before acceptance | ENFORCED | ENFORCED | ENFORCED |
| R6 | Diff cap — LOW=30, MED=50, HIGH=100 | ENFORCED | LOW=50 (relaxed) | ENFORCED |
| R7 | Dependency declaration — flag new deps | ENFORCED | ENFORCED | ENFORCED |
| R8 | Rollback — recovery points, clean commits | ENFORCED | ENFORCED | ENFORCED |

Violation routing per `enforcement/rejection-protocol.md`:
- **SOFT**: Fix the hunk, re-verify, proceed
- **HARD**: Rollback affected step (`git checkout -- .`), re-enter from Stage 7 (Planning)
- **CRITICAL** (two violations same rule): Full rollback, FAIL task, re-enter from Stage 1

---

## 4. VALIDATION GATE (CRITICAL)

The Validation Gate is a hard checkpoint that executes after Stage 8 and before Stage 10. It evaluates all accumulated output against gates H6 and H7, plus all active R rules.

### Check Sequence

```
1. H6 CHECK: Did every S5 step verify before proceeding?
   -> FAIL if any step continued past verification failure

2. H7 CHECK: Are all changes related to the task?
   -> FAIL if any line does not trace to task (R1 violation)
   -> FAIL if any addition is speculative (R2 violation)
   -> FAIL if style drifted from existing conventions (R3 violation)
   -> FAIL if LLM instructions found in code (R4 violation)

3. R5 CHECK: Were tests and linters run?
   -> FAIL if tests exist and were not run
   -> FAIL if linters exist and were not run

4. R6 CHECK: Is diff within cap?
   -> FAIL if cap exceeded without line-by-line justification

5. R7 CHECK: Were new dependencies declared in plan?
   -> FAIL if undeclared dependency found

6. R8 CHECK: Were recovery points recorded?
   -> FAIL if HEAD not noted before changes

7. TWO-STRIKE CHECK: Same rule violated twice?
   -> CRITICAL — full rollback, task FAIL
```

### Restart Conditions

If the Validation Gate detects any FAIL condition, execution MUST NOT proceed to Stage 10. Instead:

- **Single SOFT violation** → fix and re-verify, keep executing
- **HARD violation** → `git checkout -- .` for affected step, re-enter from Stage 7
- **CRITICAL** (two-strike) → `git checkout -- .` for all changes, re-enter from Stage 1
- **Missing assumptions found** → re-enter from Stage 4
- **Missing interpretations (STRICT mode)** → re-enter from Stage 5
- **Unrelated changes detected** → reject all changes, re-enter from Stage 7
- **No verification step found** → STOP, re-run verification before proceeding

DO NOT proceed past the Validation Gate if any condition fails. REJECT output that bypasses validation.

---

## 5. OUTPUT CONTRACT

Every execution MUST produce structured output in this exact format:

```
[ASSUMPTIONS]
- list all assumptions with sources

[INTERPRETATIONS]
- list all interpretations considered

[DECISION]
- selected interpretation with justification

[PLAN]
- ordered steps with verification criteria

[CHANGES]
- files modified, hunks, justifications

[VERIFICATION]
- tests, linters, diff discipline, edge cases

[VIOLATIONS]
- any rule violations and their resolution

[SCORE]
- C: /10  M: /10  A: /10  U: /10  V: /10
- Weighted: (/10)  PASS | FAIL
```

FAIL if any section is missing. FAIL if violations section omits any detected violation. FAIL if score is below threshold.

---

## 6. EXECUTION TRACE (JSON)

Every execution MUST produce a machine-readable JSON trace. Schema per `trace/output-contract.md`. Minimum required fields:

```json
{
  "schema_version": "3.0.0",
  "task": {
    "description": "",
    "type": "BUG_FIX|FEATURE|REFACTOR|EXPLORATION",
    "risk": "LOW|MED|HIGH",
    "mode": "STRICT|FAST|DEBUG"
  },
  "pipeline": {
    "stages_completed": [],
    "stages_skipped": [],
    "gates_passed": [],
    "gates_failed": []
  },
  "assumptions": [
    {
      "text": "",
      "verified": true|false,
      "source": ""
    }
  ],
  "interpretations_considered": [
    {
      "id": "",
      "description": "",
      "selected": true|false
    }
  ],
  "changes": {
    "files_modified": [],
    "total_lines": 0,
    "dependencies_added": [],
    "per_change_justifications": []
  },
  "violations": [
    {
      "rule": "R1|R2|R3|R4|R5|R6|R7|R8",
      "classification": "SOFT|HARD|CRITICAL",
      "file": "",
      "hunk": "",
      "reason": "",
      "resolution": ""
    }
  ],
  "scorecard": {
    "correctness": 0,
    "minimality": 0,
    "assumption_accuracy": 0,
    "unnecessary_complexity": 0,
    "verification": 0,
    "weighted_score": 0.0,
    "passed": true|false
  }
}
```

REJECT output if JSON is malformed. REJECT output if any required field is missing. REJECT output if scorecard shows FAIL.

---

## 7. ENFORCEMENT LANGUAGE

The following constraints govern every stage. They are not recommendations.

### Global Constraints

- ALL tasks MUST enter through the Execution Engine. No bypass.
- DO NOT skip any stage unless the active mode explicitly permits stage combining.
- DO NOT proceed past a failing gate. EVER.
- FAIL if task classification is ambiguous or missing.
- FAIL if mode contradicts risk level.
- FAIL if assumptions are unverifiable.
- FAIL if interpretations are incomplete (per mode requirements).
- FAIL if plan lacks verification criteria or backout.
- FAIL if any change lacks per-hunk justification (STRICT/DEBUG).
- FAIL if tests or linters exist and are not run.
- FAIL if unrelated changes detected.
- FAIL if diff exceeds cap without line-by-line justification.
- FAIL if dependency is added without declaration.
- FAIL if recovery points are not recorded.
- FAIL if trace output is incomplete or malformed.
- FAIL if scorecard weighted < 7 or any dimension below threshold.
- REJECT output that bypasses the Validation Gate.
- REJECT output that lacks a complete trace.

### Restart Constraints

- DO NOT restart from a later stage than the violation requires.
- Missing assumptions → restart from Stage 4, not Stage 5.
- Missing interpretations → restart from Stage 5, not Stage 6.
- Unrelated changes → restart from Stage 7, not Stage 8.
- Two-strike CRITICAL → full rollback, restart from Stage 1.

### Mode-Specific Constraints

- STRICT: All 10 stages. All 8 gates (H1-H8). Per-hunk justification required. Scorecard required. FAIL threshold: any dim < 6 or weighted < 8.
- FAST: Stages 4-6 may be combined. Gates H3/H5/H6 relaxed. Per-hunk justification optional for hunks < 10 lines. Scorecard optional. Risk escalation → switch to STRICT immediately.
- DEBUG: Extended pipeline (H0-H9). H9 requires root cause confirmed by failing test before fix. Per-hunk justification must link to root cause. Scorecard required with Root Cause Accuracy dimension.

### Output Constraints

- DO NOT output until Stage 9 (Validation Gate) passes.
- DO NOT output without structured format (Section 5).
- DO NOT output without JSON trace (Section 6).
- DO NOT output without scorecard.
- Violation of output constraints → output is REJECTED. Full rollback required.

---

*This file is the runtime. Execute it. Do not interpret it.*
