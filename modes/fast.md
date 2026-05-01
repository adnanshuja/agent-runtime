# FAST Mode

**Use when:** Risk is LOW. Task is trivial. User is experienced and wants speed.
**Motto:** "Done is better than perfect."

---

## Behavioral Modifications

### Protocol (core/protocol.md)

- All 6 stages: ABBREVIATED
- Each stage: 1-2 sentences max. No canonical format required.
- Stage 1 (Assumptions): List only critical assumptions. Can skip trivial ones.
- Stage 2 (Interpretations): Minimum 1 interpretation. Can skip alternatives if obvious.
- Stage 3 (Decision): State briefly. No full justification needed.
- Stage 4 (Plan): 1-3 steps. Verification can be "looks correct" for trivial changes.
- Stage 5 (Implementation): Still required to be minimal. No side effects.
- Stage 6 (Verification): Run tests if they exist. Can skip linting.
- Gates: NOT ENFORCED for trivial tasks. ENFORCED if risk is actually MEDIUM.

**Allowed abbreviations:**
- Combine Stage 1+2+3 into a single sentence.
- Combine Stage 4+5 into a single action.
- Skip Stage 6 if task is purely cosmetic (typo fix, formatting).

### Enforcement (core/enforcement.md)

- R1 (No unrelated modifications): ENFORCED — still must be minimal.
- R2 (No speculative generality): ENFORCED — still no future-proofing.
- R3 (No style drift): RELAXED — minor style matching is fine but not strict.
- R4: ENFORCED.
- R5: RELAXED — run tests if they exist and are fast. Skip slow tests.
- R6: RELAXED — >50 lines requires brief justification (1 sentence).
- R7: ENFORCED — still no hidden deps.
- R8: RELAXED — note HEAD but don't require formal backout plan.

### Context Classification

- Brief classification only: state task type + risk in one line.
- No matrix lookup required.
- Can default to LOW risk.

### Self-Evaluation

- Post-task scoring OPTIONAL.
- If done: brief format, no evidence links required.
- No FAIL threshold.

### User Interaction

- Do not pause between stages unless blocked.
- Do not ask confirming questions unless risk is misclassified.

---

## When FAST Mode is FORBIDDEN

FAST mode is FORBIDDEN when:
- Risk level is HIGH (data loss, production, security)
- Task is a REFACTOR of HIGH risk
- User explicitly requested STRICT mode
- Operating on an unfamiliar codebase
