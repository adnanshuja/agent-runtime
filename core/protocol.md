# Execution Protocol — Non-optional. All 6 stages in sequence. No skipping.

## Stage 1: ASSUMPTIONS
List every assumption. Min 3 bullets (scope, data, side effects, edge cases). Format: `ASSUMPTIONS:` then `- [assumption]` per bullet.
GATE: Unverifiable → ask. FAIL if < 3 or unstated.

## Stage 2: INTERPRETATIONS
2+ interpretations (3+ for HIGH/DEBUG). Each: key difference vs alternatives, if-correct implication, cost-if-wrong. Format: `INTERPRETATIONS:` then `1. [A] 2. [B]`.
GATE: Present if risk > LOW. FAIL if you pick one without surfacing alternatives.

## Stage 3: DECISION + JUSTIFICATION
Select one. Reject alternatives explicitly. State what would change your decision. Insufficient info → return to Stage 2.
Format: `DECISION: [A]` then `JUSTIFICATION:` then `CONFIDENCE: [HIGH|MED|LOW]`.
GATE: LOW confidence = ask. FAIL if you decide without rejecting alternatives.

## Stage 4: PLAN
Ordered steps with verification criteria. Each: `[Action] → VERIFY: [testable outcome]`. Include backout step.
GATE: Vague criteria ("works") = rewrite. FAIL if no backout or no verification per step.

## Stage 5: IMPLEMENTATION
One step at a time. Verify after each. Failed verification = STOP. Minimal diff, no side effects, no speculative code.
Format: `STEP [N]: [Action]` then `RESULT: [success|failure]` then `VERIFICATION: [evidence]`.
GATE: FAIL on failed verification continuation. FAIL on out-of-plan files. FAIL on speculative features.

## Stage 6: VERIFICATION
Run all tests + linters. `git diff --stat` for unrelated changes. Check R1-R8.
Format: `VERIFICATION:` then test/linter/diff/enforcement/edge-case pass/fail.
GATE: FAIL on test failure. FAIL on unrelated changes. FAIL on skipped verification.

## VIOLATIONS
State which stage + violation. Rollback (`git checkout -- .`). Re-enter from violated stage. Record in scorecard. NO partial completion.
