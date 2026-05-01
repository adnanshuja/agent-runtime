# LLM Execution Protocol

**An operating system for coding agents.**

This system enforces structured, verifiable, and minimal LLM code execution through a modular protocol with hard constraints, context-aware mode selection, and post-execution self-evaluation.

Built for: **Claude Code** · **Cursor** · **Antigravity** · **VS Code** · **Any LLM coding tool**

---

## Core Philosophy

LLMs fail at code generation in predictable ways:
- They make assumptions instead of asking
- They overengineer instead of simplifying
- They modify unrelated code instead of being surgical
- They skip verification instead of confirming

This system treats those failures as **protocol violations**, not quirks.

## What This System Provides

| Component | What It Does |
|-----------|-------------|
| **6-Stage Protocol** | Assumptions → Interpretations → Decision → Plan → Implementation → Verification |
| **3 Operation Modes** | STRICT (safe), FAST (quick), DEBUG (investigative) |
| **Context Classifier** | Detects task type + risk, adapts behavior automatically |
| **8 Enforcement Rules** | Hard constraints with rejection criteria, not soft suggestions |
| **Self-Evaluation** | 5-dimension scoring after every task |
| **Platform Integrations** | Claude Code plugin, Cursor rules, Antigravity, VS Code |

---

## Quick Start

### Claude Code (Recommended)

```bash
# Install the plugin
/plugin marketplace add adnanshuja/llm-execution-protocol
/plugin install llm-execution-protocol@llm-execution-protocol

# Use it
Use LLM Execution Protocol in STRICT mode.
```

### Cursor

Copy `.cursor/rules/llm-execution-protocol.mdc` into your project's `.cursor/rules/` directory.

### VS Code

See `integrations/vscode.md` for Copilot, Cody, and custom instructions setup.

### Any LLM

Copy the contents of `CLAUDE.md` and `core/protocol.md` into your system prompt.

---

## Mode Selection

```
STRICT — Full protocol, all gates enforced. Use for MED/HIGH risk.
FAST   — Abbreviated protocol. Use for LOW risk, trivial tasks.
DEBUG  — Extended investigation. Use for bug reproduction, root cause analysis.
```

Default: STRICT. Switch with "Use [MODE] mode" in your prompt.

---

## Repository Structure

```
/
├── CLAUDE.md                    Root entry point
├── README.md                    This file
├── core/
│   ├── protocol.md              6-stage execution protocol
│   ├── enforcement.md           8 hard constraint rules
│   └── self-evaluation.md       Post-task scoring
├── context/
│   ├── classifier.md            Task type + risk classification
├── modes/
│   ├── strict.md                STRICT mode definition
│   ├── fast.md                  FAST mode definition
│   └── debug.md                 DEBUG mode definition
├── extensions/
│   ├── antigravity/             Antigravity agent rules
│   └── vscode/                  VS Code snippets and config
├── examples/
│   ├── bad-vs-good.md           Protocol vs no-protocol comparisons
│   └── diff-comparisons.md      Clean vs contaminated diffs
├── integrations/
│   ├── claude-code.md           Claude Code setup
│   ├── cursor.md                Cursor setup
│   ├── antigravity.md           Antigravity setup
│   └── vscode.md                VS Code setup
├── .cursor/rules/               Cursor-specific rule file
├── .antigravity/rules/          Antigravity-specific rule file
└── .claude-plugin/              Claude Code plugin definition
```

---

## Requirements

- **Claude Code** (plugin integration) or
- **Cursor** (rules integration) or
- Any LLM tool that accepts system prompts

---

## License

MIT
