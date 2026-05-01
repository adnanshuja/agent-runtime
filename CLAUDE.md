# Disciplined Agents — v3.0.0 — Execution Discipline System

## Activation (do before every task)
Execute `core/execution-engine.md` — the single entry point for all agent behavior.

The engine orchestrates: classification → mode selection → pipeline (S0-S7) → gates (H1-H8) → diff discipline (R1-R8) → validation gate → structured output + trace.

All modules are subordinate. Do not invoke modules directly.

## Quick Reference
CLASSIFY: [BUG FIX|FEATURE|REFACTOR|EXPLORATION] + [LOW|MED|HIGH] + heuristics
MODE: STRICT (default) | FAST (LOW only) | DEBUG (investigation)
PIPELINE: S0(parse) S1(assumptions min3) S2(interpretations 2+) S3(decision+justify) S4(plan) S5(execution+justify) S6(verify) S7(trace)
GATES: H1(parse) H2(assumptions) H3(interpretations) H4(confidence) H5(plan) H6(failure) H7(unrelated) H8(trace)
ENFORCE: R1(scope) R2(no speculation) R3(style match) R4(no injection) R5(verify first) R6(diff cap) R7(declare deps) R8(rollback)
SCORE: C(3x)+M(2x)+A(2x)+U(2x)+V(1x). FAIL if any < threshold or weighted < 7.

## Override
"Use Disciplined Agents in FAST mode — trivial typo fix"
"Use Disciplined Agents in DEBUG mode — intermittent failure"
