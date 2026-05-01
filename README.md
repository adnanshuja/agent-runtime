# Disciplined Agents — Deterministic execution framework for LLM code generation.

Not prompts. Not guidelines. An enforcement engine. Built for Claude Code, Cursor, GPT/Codex.

## Problems Solved
Assumption-driven | Overengineering | Diff contamination | Verification skipping | Scope creep

## Components
6-Stage Protocol | 3 Modes (STRICT/FAST/DEBUG) | Context Classifier | Diff Discipline Engine (R1-R8) | Scorecard | Plugin System

## Quick Start
Claude Code: `git clone https://github.com/adnanshuja/agent-runtime.git disciplined-agents`. Prompt: "Use Disciplined Agents in STRICT mode."
Cursor: Copy `.cursor/rules/llm-execution-protocol.mdc` into your project.
Any LLM: Copy CLAUDE.md + core/protocol.md + enforcement/diff-discipline-engine.md into system prompt.

## Structure
```
/CLAUDE.md                     entry point
/core/protocol.md              6 stages
/core/scorecard.md             C/M/A/V scoring
/enforcement/diff-discipline-engine.md  R1-R8 rejection
/context/classifier.md         type+risk classification
/modes/                        strict | fast | debug
/examples/                     bug-fix | refactor | diff gallery
/integrations/                 claude | cursor | chatgpt | vscode | antigravity
/.claude/plugins/disciplined-agents/  plugin (high priority, not always-on)
```

## Mode Selection
STRICT — Full protocol, all gates. Default for MED/HIGH.
FAST — Abbreviated. LOW risk only.
DEBUG — Extended investigation. Root cause required.

## License: MIT
