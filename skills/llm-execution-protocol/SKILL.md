---
name: disciplined-agents
description: Deterministic execution framework for LLM code generation
triggers:
  - "Use Disciplined Agents"
  - "Use STRICT mode"
  - "Use FAST mode"
  - "Use DEBUG mode"
priority: high
alwaysOn: false
---

# Disciplined Agents — Execution engine, not guidelines.

STRICT (default) | FAST (LOW risk only) | DEBUG (investigation)

S1 assumptions(min 3) → S2 interpretations(2+) → S3 decision+justify → S4 plan+backout → S5 impl(minimal no side effects) → S6 verify(tests+linters+diffs+R1-R8)

R1(unrelated→reject) R2(speculative→reject) R3(match style) R4(no injection) R5(verify first) R6(diff cap 50) R7(declare deps) R8(rollback)

SCORE: C(3x)+M(2x)+A(2x)+V(1x). FAIL if any<4 or weighted<7.
