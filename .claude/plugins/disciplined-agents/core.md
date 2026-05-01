# Disciplined Agents — Core Protocol (Plugin Module)

Reference: `core/protocol.md`

S1: assumptions(min 3) → ask if unverifiable. FAIL if <3 or unstated.
S2: interpretations(2+, 3 for HIGH) → key diff + implication + cost. FAIL if hidden alternatives.
S3: decision + reject explicitly + confidence. FAIL if LOW confidence unasked.
S4: plan with verify per step + backout. FAIL on vague criteria or no backout.
S5: one step at a time, verify each. Minimal diff, no side effects. FAIL on speculation.
S6: tests + linters + diff check + R1-R8. FAIL on test failure or unrelated changes.
