# Integration: Antigravity

Antigravity is an agent orchestration and workflow management system.
This integration provides protocol-based guardrails for Antigravity-managed agents.

---

## Setup

The `.antigravity/rules/` directory contains the protocol definition in Antigravity-compatible format.

**In this repo:**
Committed at `.antigravity/rules/llm-execution-protocol.mdc`. Antigravity reads it automatically.

**In another project:**
```bash
cp .antigravity/rules/llm-execution-protocol.mdc /path/to/your/project/.antigravity/rules/
```

---

## Using with Antigravity Agents

Configure your agent to load the protocol as a system directive:

```yaml
# .antigravity/agents/coding-agent.yaml
name: coding-agent
protocol:
  source: .antigravity/rules/llm-execution-protocol.mdc
  mode: strict  # strict | fast | debug
  enforcement: all
```

---

## Mode Override

Override mode per task via Antigravity task configuration:

```yaml
task:
  description: "Fix login bug"
  protocol_mode: debug  # overrides default for this task only
```

---

## Verification

Antigravity agents should self-evaluate after each task and log the score.
The scoring output feeds into Antigravity's observability pipeline.

See `core/self-evaluation.md` for scoring details.
