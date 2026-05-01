# LLM Execution Protocol

**Version:** 2.0.0
**Mode:** STRICT (default) — override with FAST or DEBUG as needed.

This system contains the protocol definition, enforcement rules, and integration guides for structured LLM code execution.

**Before every task, run classification:**
1. Read `context/classifier.md` — classify task type + risk level
2. Read `modes/` — select the appropriate mode
3. Read `core/protocol.md` — execute the 6-stage protocol
4. Read `core/enforcement.md` — apply enforcement rules
5. Read `core/self-evaluation.md` — score after completion

---

## Quick Start

```
New task detected. Running classification...
```

1. **Classify:** [BUG FIX | FEATURE | REFACTOR | EXPLORATION] + [LOW | MED | HIGH] risk
2. **Mode:** [STRICT | FAST | DEBUG]
3. **Protocol:** Execute Stages 1-6 from `core/protocol.md`
4. **Enforce:** Apply R1-R8 from `core/enforcement.md`
5. **Evaluate:** Score from `core/self-evaluation.md`

---

## File Index

| File | Purpose |
|------|---------|
| `core/protocol.md` | Non-optional 6-stage execution protocol |
| `core/enforcement.md` | 8 hard constraint rules with rejection criteria |
| `core/self-evaluation.md` | 5-dimension post-task scoring system |
| `context/classifier.md` | Task type + risk level classification |
| `modes/strict.md` | Full protocol mode — safe, slow, thorough |
| `modes/fast.md` | Minimal protocol mode — speed optimized |
| `modes/debug.md` | Deep investigation mode — root cause focus |
| `examples/bad-vs-good.md` | Real examples: protocol vs no protocol |
| `examples/diff-comparisons.md` | Clean vs contaminated diff comparisons |
| `integrations/claude-code.md` | Plugin + CLAUDE.md setup |
| `integrations/cursor.md` | Cursor rules setup |
| `integrations/antigravity.md` | Antigravity agent orchestration setup |
| `integrations/vscode.md` | VS Code / Copilot / Cody setup |

---

## Mode Override

To switch modes, say:

```
Use FAST mode — this is a trivial typo fix.
Use DEBUG mode — the payment flow is broken intermittently.
```

Without an explicit override, STRICT mode is the default.
