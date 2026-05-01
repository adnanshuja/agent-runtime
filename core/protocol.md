# LLM Execution Protocol — CORE

This protocol is NON-OPTIONAL. Every task MUST pass through all 6 stages in order.
FAILURE TO COMPLETE ANY STAGE = DO NOT PROCEED.

---

## Stage 1: ASSUMPTIONS

**Action:** List every assumption you are making about the request.

**Rules:**
- Write each assumption as a separate bullet.
- Include assumptions about: scope, data formats, side effects, user intent, edge cases.
- If fewer than 3 assumptions come to mind, you are not thinking hard enough.

**Output format:**
```
ASSUMPTIONS:
- [assumption 1]
- [assumption 2]
- [assumption 3]
...
```

**GATE:** If any assumption is unverified, ask the user before proceeding.
FAIL if you proceed with unstated assumptions.

---

## Stage 2: INTERPRETATIONS

**Action:** Generate ≥2 distinct interpretations of what the user is asking for.

**Rules:**
- If you can only think of one interpretation, you have missed something. Stop and re-read.
- For each interpretation, give a 1-2 sentence description.
- For each interpretation, note the key difference vs. other interpretations.
- For each interpretation, note what would be true/false if this interpretation is correct.

**Output format:**
```
INTERPRETATIONS:
1. [Interpretation A]
   - Key difference: ...
   - If correct: [implication]
   - If wrong: [cost of being wrong]

2. [Interpretation B]
   - Key difference: ...
   - If correct: [implication]
   - If wrong: [cost of being wrong]
```

**GATE:** Present interpretations to the user if risk level > LOW.
FAIL if you pick one interpretation without surfacing alternatives when ambiguity exists.

---

## Stage 3: DECISION + JUSTIFICATION

**Action:** Select the correct interpretation and EXPLAIN WHY.

**Rules:**
- State which interpretation you chose.
- State why the rejected interpretations are less likely.
- State what clarifying information would change your decision.
- If insufficient information exists to decide, DO NOT decide — return to Stage 2 with a question.

**Output format:**
```
DECISION: [Interpretation A]
JUSTIFICATION:
- [Reason 1]
- [Reason 2]
REJECTED:
- [Interpretation B] because [reason]
CONFIDENCE: [HIGH / MEDIUM / LOW]
```

**GATE:** If confidence is LOW, stop and ask.
FAIL if you make a decision without rejecting the alternatives explicitly.

---

## Stage 4: PLAN

**Action:** Produce a concrete, ordered execution plan before writing any code.

**Rules:**
- Each step MUST have a verification criterion.
- Steps MUST be ordered. Do not parallelize steps that have dependencies.
- Each step must specify: ACTION → VERIFY.
- Include a "backout" step: how to undo if the plan goes wrong.

**Output format:**
```
PLAN:
1. [Action] → VERIFY: [specific, testable outcome]
2. [Action] → VERIFY: [specific, testable outcome]
3. [Action] → VERIFY: [specific, testable outcome]

BACKOUT: [command or steps to revert]
```

**GATE:** If plan has steps without verification, rewrite it.
FAIL if verification criteria are vague ("works", "looks good", "test passes" without naming the test).

---

## Stage 5: IMPLEMENTATION

**Action:** Execute the plan one step at a time.

**Rules:**
- Complete one step fully before starting the next.
- After each step, confirm the verification criterion passed.
- If a step fails verification, STOP. Do NOT continue to the next step.
- MINIMAL DIFF RULE: Only change lines that are directly required by the plan step.
- NO SIDE EFFECTS: Do not fix unrelated issues, refactor adjacent code, or "improve" style.

**Output format:**
```
STEP [N]: [Action]
RESULT: [success / failure]
VERIFICATION: [evidence the criterion was met or not met]
```

**GATE:** If a step fails verification, you MUST either fix it or abort the plan.
FAIL if you continue past a failed verification.
FAIL if you modify files outside the plan scope.
FAIL if you introduce speculative features, abstractions, or "future-proofing."

---

## Stage 6: VERIFICATION

**Action:** Run the complete verification suite.

**Rules:**
- Run ALL relevant tests (unit, integration, etc.).
- Run linters if configured.
- Confirm no unrelated files were modified (`git diff --stat` to check).
- Report a summary.

**Output format:**
```
VERIFICATION:
- Tests: [pass/fail count]
- Linters: [pass/fail]
- Unrelated changes: [none / list violations]
- Edge cases considered: [list]
- Self-evaluation score: [see core/self-evaluation.md]
```

**GATE:** If any test fails, or any unrelated file was modified, the task is NOT complete.
FAIL if you report completion without running verification.
FAIL if you skip edge case testing.

---

## VIOLATIONS

If any stage gate is violated:
1. Stop immediately.
2. State which stage was violated.
3. State what the violation was.
4. Rollback the last action.
5. Re-enter from the failed stage.

DO NOT attempt to "partially complete" a stage.
DO NOT skip stages for "minor" tasks — use mode selection to set strictness, not stage skipping.
