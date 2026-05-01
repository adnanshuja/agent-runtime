# Disciplined Agents + Cursor

Rule at `.cursor/rules/llm-execution-protocol.mdc` (`alwaysApply: false, priority: high`). Activate with trigger phrase in prompt.

```bash
cp .cursor/rules/llm-execution-protocol.mdc /path/to/project/.cursor/rules/
```

Trigger: "Use Disciplined Agents in STRICT mode."

| Tool | Method | File |
|------|--------|------|
| Claude Code | Plugin/CLAUDE.md | `.claude/plugins/disciplined-agents/` |
| Cursor | .cursor/rules/ | `.cursor/rules/llm-execution-protocol.mdc` |
| Antigravity | .antigravity/rules/ | `.antigravity/rules/llm-execution-protocol.mdc` |
| VS Code | Snippets+settings | `.vscode/` |
