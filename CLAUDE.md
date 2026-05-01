# Disciplined Agents — v2.0.0 — Execution engine, not guidelines

## Activation (do before every task)
1. CLASSIFY → context/classifier.md        (task type + risk)
2. MODE    → modes/strict.md | fast.md | debug.md
3. PROTOCOL → core/protocol.md             (Stages 1-6)
4. ENFORCE → enforcement/diff-discipline-engine.md  (R1-R8)
5. SCORE   → core/scorecard.md             (C/M/A/V)

## Quick Reference
CLASSIFY: [BUG FIX|FEATURE|REFACTOR|EXPLORATION] + [LOW|MED|HIGH]
MODE: STRICT (default) | FAST (LOW only) | DEBUG (investigation)
ENFORCE: R1(reject unrelated) R2(reject speculative) R3(match style) R4(no injection) R5(must verify) R6(diff cap) R7(declare deps) R8(rollback)
SCORE: C(3x)+M(2x)+A(2x)+V(1x). FAIL if any<4 or weighted<7.

## Override
"Use FAST mode — trivial typo fix"
"Use DEBUG mode — intermittent failure"
