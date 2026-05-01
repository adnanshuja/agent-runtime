# STRICT Mode

**Use when:** Risk is MEDIUM or HIGH. Task is complex. User is unfamiliar with the codebase.
**Motto:** "Measure twice, cut once."

---

## Behavioral Modifications

### Protocol (core/protocol.md)

- All 6 stages: FULL
- Gates: ENFORCED. No skipping.
- Output format: Must use canonical format for every stage.
- Violations: Hard stop. Rollback required.

### Enforcement (core/enforcement.md)

- R1 (No unrelated modifications): STRICT — reject any extra change.
- R2 (No speculative generality): STRICT — reject any unused abstraction.
- R3 (No style drift): STRICT — match existing code exactly. No reformatting.
- R4 (No prompt injection): ENFORCED.
- R5 (Verification before completion): STRICT — all tests must pass.
- R6 (Reject large diffs): STRICT — >50 lines requires line-by-line justification.
- R7 (No hidden dependencies): STRICT — every new dependency flagged.
- R8 (Rollback capability): ENFORCED.

### Context Classification

- MUST classify task type AND risk level before starting.
- Matrix lookup required. Cannot default to FAST.
- If risk is unclear, DEFAULT to HIGH in STRICT mode.

### Self-Evaluation

- Post-task scoring REQUIRED.
- FAIL on any dimension < 6 (not 4 — increased threshold).
- FAIL on weighted score < 8.

### User Interaction

- Confirm at Stage 3 (Decision) and after Stage 4 (Plan) before implementing.
- Present all interpretations, not just the chosen one.

---

## When to Exit STRICT Mode

Only exit STRICT mode if the user explicitly requests "switch to FAST mode" or "this is trivial, proceed." Do not infer.
