# DEBUG Mode

**Use when:** Investigating a bug, performance issue, or unexpected behavior.
**Motto:** "Trust nothing. Verify everything."

---

## Behavioral Modifications

### Protocol (core/protocol.md)

- Stage 1 (Assumptions): EXTENDED — list assumptions about what could NOT be the cause too.
- Stage 2 (Interpretations): EXTENDED — ≥3 interpretations. Include "long shot" interpretations.
- Stage 3 (Decision): DEFERRED — do NOT commit to an interpretation until investigation yields evidence.
- Stage 4 (Plan): INVESTIGATION PLAN, not implementation plan. Steps are probes, not changes.
- Stage 5 (Implementation): IMPLEMENTATION IS FORBIDDEN until root cause is confirmed.
- Stage 6 (Verification): PROOF, not tests. Must demonstrate root cause conclusively.

**Additional debug-only stage:**

### Stage 0: REPRODUCE

Before any analysis, reproduce the issue.
- Exact steps to reproduce.
- Environment (OS, versions, config).
- Frequency (100%? intermittent?).
- If you cannot reproduce, STOP. Do not guess.

### Stage 2.5: HYPOTHESIS TREE

After interpretations, build a tree of possible causes:

```
BUG: [description]
├── Root cause category A
│   ├── Hypothesis A1
│   └── Hypothesis A2
├── Root cause category B
│   ├── Hypothesis B1
│   └── Hypothesis B2
└── Root cause category C
    └── Hypothesis C1
```

Each hypothesis must be falsifiable (you can prove it wrong with a test/probe).

### Stage 2.75: ELIMINATION

For each hypothesis:
- Run a probe that would prove it WRONG.
- If probe proves it wrong → cross it off.
- If probe doesn't prove it wrong → it remains a candidate.
- Report confidence level for remaining candidates.

### Stage 3.5: ROOT CAUSE CONFIRMATION

Before implementing any fix:
- State the confirmed root cause.
- State why it wasn't caught by existing tests.
- State what test would have caught it.
- Create that test, confirm it fails.

### Stage 5: IMPLEMENTATION (only after root cause confirmed)

- Fix the root cause. Do NOT patch symptoms.
- The test from Stage 3.5 must pass after the fix.
- Check for the same pattern elsewhere (inform of other occurrences but only fix what's asked).

### Stage 6: VERIFICATION (extended)

- Confirm the fix doesn't break existing tests.
- Confirm the reproduction steps from Stage 0 no longer produce the bug.
- If performance bug: benchmark before/after.

### Enforcement (core/enforcement.md)

- R1-R8: FULLY ENFORCED same as STRICT mode.
- Additionally: R0 — No fixing without root cause confirmation. Fixing without root cause confirmed is a VIOLATION.

### Context Classification

- Task type: BUG FIX (always — DEBUG mode reframes the approach, not the task type).
- Risk: MEDIUM or HIGH. DEBUG with LOW risk is usually unnecessary.

### Self-Evaluation

- REQUIRED.
- Extra dimension: ROOT CAUSE ACCURACY — was the identified root cause the actual cause?
  - Score 10: Root cause confirmed with evidence. Reproduction test written.
  - Score 0: Fixed a symptom. Root cause not found.

### User Interaction

- Report findings after each hypothesis elimination round.
- MUST confirm root cause with user before implementing fix.
- If investigation is inconclusive, report what was ruled out and ask for next steps.
