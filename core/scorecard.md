# Scorecard — Every task. FAIL if any<4 or weighted<7.

C Correctness(3x): 10=all reqs+edge cases+tests pass. 0-4=bugs/wrong problem.
M Minimality(2x): 10=every line traces to task. 0-4=unrelated/speculative.
A Assumption accuracy(2x): 10=all verified+correct. 0-4=wrong from bad assumptions.
V Verification(1x): 10=tests+linters+edges+diffs. 0-2=no verification.

SCORE = (C*3 + M*2 + A*2 + V*1) / 8
PASS >= 7 AND all >= 4

LOG: TASK:[desc] SCORE:[w](PASS/FAIL) DIMS:C=[/10] M=[/10] A=[/10] V=[/10] VIOL:[R1-R8] LEARN:[improvement]

FAIL → rollback all (`git checkout -- .`). Restart from S1.
