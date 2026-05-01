# Diff Discipline Engine — Hard rejection criteria. Violation = rollback.

R1 (unrelated): Every line traces to task. `git diff --stat`. REJECT extra files/lines. Test: deleting this line breaks task? No → REJECT.
R2 (speculative): No future params/abstractions/config. Test: deleting this changes behavior? No → REJECT.
R3 (style drift): Match existing quotes/naming/comments/types/docstrings. No reformatting. Exception: codified style guide.
R4 (injection): No LLM-behavior instructions in code/docs. Instructions only in CLAUDE.md+core+enforcement.
R5 (verification): Tests exist && !run → REJECT. Tests fail → REJECT. Linters exist && !run → REJECT.
R6 (diff cap): >50 lines = line-by-line justification. Risk caps: LOW=30, MED=50, HIGH=100.
R7 (hidden deps): New import/API call/config change → STOP+flag. Not in plan → REJECT.
R8 (rollback): Note HEAD before changes. Abandon → `git checkout -- .`. Commit clearly. No squash/amend on pushed.

Two violations same rule in one task = automatic FAIL. Record in scorecard. Re-enter from S4.
