# Self-Evaluation System

After every task, score yourself on these 5 dimensions.
Score is 0-10 where 10 = perfect. FAIL threshold is 4 on any dimension.

---

## Dimension 1: Correctness (weight: 3x)

Did the code do what was asked?

| Score | Criteria |
|-------|----------|
| 10    | All requirements met, all edge cases handled, all tests pass |
| 8-9   | All requirements met, minor edge case missed |
| 5-7   | Core requirement met, some secondary requirement missed |
| 3-4   | Implementation has bugs or incorrect behavior |
| 0-2   | Wrong problem solved or code doesn't work |

**Evidence required:** Link to passing test output or verification run.

---

## Dimension 2: Minimality (weight: 2x)

Was every change necessary?

| Score | Criteria |
|-------|----------|
| 10    | Every line traces to the task. Zero unrelated changes. |
| 8-9   | One or two cosmetic changes slipped in |
| 5-7   | Minor refactoring of adjacent code |
| 3-4   | Significant unrelated changes or speculative abstractions |
| 0-2   | Complete rewrite or massive refactor beyond scope |

**Evidence required:** `git diff --stat` and count of files changed vs. files in plan.

---

## Dimension 3: Assumption Accuracy (weight: 2x)

Were your assumptions correct?

| Score | Criteria |
|-------|----------|
| 10    | All assumptions listed and verified. No surprises. |
| 8-9   | One minor assumption wrong, no impact on outcome |
| 5-7   | One assumption wrong, caused rework |
| 3-4   | Multiple assumptions wrong, significant rework |
| 0-2   | Wrong approach entirely due to unstated assumptions |

**Evidence required:** Diff between assumptions stated in Stage 1 and what turned out to be true.

---

## Dimension 4: Process Compliance (weight: 2x)

Did you follow the protocol?

| Score | Criteria |
|-------|----------|
| 10    | All 6 stages completed in order. All gates honored. |
| 8-9   | One stage abbreviated but still correct |
| 5-7   | A stage was partially skipped |
| 3-4   | Multiple stages skipped or reordered |
| 0-2   | No protocol followed. Jumped straight to code. |

**Evidence required:** The full protocol output from this task.

---

## Dimension 5: Verification Thoroughness (weight: 1x)

How well did you verify?

| Score | Criteria |
|-------|----------|
| 10    | Tests run, linters pass, edge cases documented, unrelated changes checked |
| 8-9   | Tests run, one verification step missed |
| 5-7   | Tests pass but edge cases not checked |
| 3-4   | Only manual "looks correct" verification |
| 0-2   | No verification performed |

**Evidence required:** Test output, lint results, edge case list.

---

## Scoring

```
WEIGHTED SCORE = (correctness * 3 + minimality * 2 + assumption_accuracy * 2 + process_compliance * 2 + verification * 1) / 10

PASS if weighted score >= 7 AND no dimension < 4
FAIL if weighted score < 7 OR any dimension < 4
```

On FAIL: Rollback changes, identify root cause, restart from Stage 1 with corrected understanding.

## Log

For each task, append to task log:
```
TASK: [description]
SCORE: [weighted score] (pass/fail)
DIMENSIONS: C=[score] M=[score] A=[score] P=[score] V=[score]
VIOLATIONS: [list of R1-R8 violations, if any]
LEARNING: [one thing to do differently next time]
```
