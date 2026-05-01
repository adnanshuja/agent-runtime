# Disciplined Agents + Antigravity

Rule at `.antigravity/rules/llm-execution-protocol.mdc`. Copy to another project: `cp .antigravity/rules/llm-execution-protocol.mdc /path/to/project/.antigravity/rules/`.

Configure agent:
```yaml
protocol:
  source: .antigravity/rules/llm-execution-protocol.mdc
  mode: strict  # strict | fast | debug
  enforcement: all
```

Mode override per task:
```yaml
task:
  protocol_mode: debug  # overrides default for this task
```

Scorecard results feed into Antigravity observability. See `core/scorecard.md`.
