---
name: llm-execution-protocol
description: Structured execution protocol for LLM coding agents. Enforces assumptions, interpretations, planning, minimal diffs, verification, and self-evaluation. Use for any non-trivial coding task.
license: MIT
---

# LLM Execution Protocol

An operating system for coding agents. Structured, enforceable, modular.

## Default Mode: STRICT

Override with "Use FAST mode" or "Use DEBUG mode" as needed.

## Core Protocol (6 Stages — Non-Optional)

1. **Assumptions** — List every assumption. Ask if uncertain.
2. **Interpretations** — ≥2 alternatives. Surface what you're deciding between.
3. **Decision + Justification** — Choose one. Explain why. State confidence.
4. **Plan** — Ordered steps with verification criteria. Backout plan included.
5. **Implementation** — One step at a time. Verify after each. No side effects.
6. **Verification** — Tests, linters, diff check. If any fail, task is not done.

## Enforcement

- No unrelated file modifications
- No speculative abstractions or future-proofing
- No style drift (match existing code)
- No task completion without verification
- Diffs >50 lines require line-by-line justification
- All changes must be revertible

## Self-Evaluation

Score after every task (0-10):
- Correctness (3x weight)
- Minimality (2x)
- Assumption accuracy (2x)
- Process compliance (2x)
- Verification thoroughness (1x)

Pass: weighted score ≥ 7 AND no dimension < 4.
Fail: rollback changes, restart from Stage 1.

## Modes

| Mode | When | Behavior |
|------|------|----------|
| STRICT | MED/HIGH risk, complex tasks | Full protocol, all gates enforced |
| FAST | LOW risk, trivial tasks | Abbreviated stages, relaxed gates |
| DEBUG | Bug investigation | Extended reproduction, hypothesis tree, no premature fixes |

## Integration

- **Claude Code:** Install via plugin marketplace
- **Cursor:** Copy `.cursor/rules/llm-execution-protocol.mdc`
- **Antigravity:** Copy `.antigravity/rules/llm-execution-protocol.mdc`
- **VS Code:** Use `.vscode/settings.json` for Copilot/Cody instructions
- **Any LLM:** Paste core protocol into system prompt
