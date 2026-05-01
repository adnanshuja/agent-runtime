# LLM Execution Protocol + Cursor

This directory contains the Cursor integration for the LLM Execution Protocol.

---

## Setup

**In this repo:**
The rule at `.cursor/rules/llm-execution-protocol.mdc` is committed with `alwaysApply: true`.
Open this folder in Cursor — the protocol activates automatically.

**In another project:**
Copy the rule file:
```bash
# From this repo
cp .cursor/rules/llm-execution-protocol.mdc /path/to/your/project/.cursor/rules/

# Or download
curl -o .cursor/rules/llm-execution-protocol.mdc \
  https://raw.githubusercontent.com/adnanshuja/llm-execution-protocol/main/.cursor/rules/llm-execution-protocol.mdc
```

Verify in Cursor: Settings → Rules → `llm-execution-protocol` should appear.

---

## Mode Selection in Cursor

Tell Cursor which mode to use in your chat prompt:

```
[STRICT] Add validation to the payment form
[FAST] Fix the typo in the error message
[DEBUG] Investigate why the cache is returning stale data
```

Without a prefix, Cursor defaults to STRICT mode (defined in the rule file).

---

## Claude Code vs Cursor vs Other Tools

| Tool | Integration Method | File |
|------|-------------------|------|
| Claude Code | Plugin / CLAUDE.md | `.claude-plugin/` |
| Cursor | `.cursor/rules/` rule | `.cursor/rules/llm-execution-protocol.mdc` |
| Antigravity | `.antigravity/rules/` rule | `.antigravity/rules/llm-execution-protocol.mdc` |
| VS Code | `.vscode/` snippets + settings | `.vscode/` |

Each integration mirrors the same core protocol. Behavior is consistent across tools.
