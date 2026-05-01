# Context Classifier — Classify before every task. FAIL if you skip.

## Task Types
BUG FIX: Reproduction test → root cause → fix. Check same pattern elsewhere (report only). Default MED.
FEATURE: Define min scope → match patterns → verify no regression. Default MED.
REFACTOR: BEFORE/AFTER equivalence provable. Tests first. Incremental. Default HIGH.
EXPLORATION: Investigate only. No impl without approval. Default LOW.

## Risk Levels
LOW: Single file, trivial, fully reversible. → FAST. Abbreviated. No pauses.
MED: Multi-file, logic changes, regression risk. → STRICT. Full protocol. Confirm S3.
HIGH: Destructive, data/security/prod/deps/CI. → STRICT. Full. Confirm every gate. FAST forbidden.

## Behavioral Matrix
| TYPE       | LOW              | MED               | HIGH                |
|------------|------------------|-------------------|---------------------|
| BUG FIX    | FAST, confirm S1 | STRICT, confirm S3| STRICT, confirm all |
| FEATURE    | FAST, confirm S1 | STRICT, confirm S3| STRICT, confirm all |
| REFACTOR   | FAST, confirm S4 | STRICT, confirm S4| STRICT, confirm all |
| EXPLORATION| FAST, report     | FAST, report      | FAST, report        |

DEFAULT: BUG FIX + MEDIUM + STRICT
