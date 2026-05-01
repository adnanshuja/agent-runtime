# DEBUG — Bug investigation, performance, unexpected behavior.

S0 REPRODUCE: Before any analysis. Exact steps+env+frequency. Cannot reproduce → STOP. No speculative fixes.
S1: Add "what could NOT be the cause" to assumptions.
S2: 3+ interpretations. Include long shots.
S2.5 HYPOTHESIS TREE: Categories → hypotheses. Each must be falsifiable (provable wrong).
S2.75 ELIMINATION: Probe each to prove it WRONG. Cross off eliminated. Report remaining confidence.
S3: Deferred. No commitment until evidence.
S3.5 ROOT CAUSE: Confirm cause + evidence + why tests missed it. Create failing test.
S5: Fix root cause, not symptom. Fail test must pass.
S6: Extended. Confirm reproduction gone. Benchmark if perf.

Enforcement: R0 (root cause required before fix). R1-R8 same as STRICT.
Scorecard: Required. Extra dim: Root Cause Accuracy.
User: Report after each elimination round. Confirm root cause before fix.
