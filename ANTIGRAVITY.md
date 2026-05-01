# LLM Execution Protocol + Antigravity

Antigravity is an agent orchestration platform for managing coding agents across workflows.
This integration adds the LLM Execution Protocol as a guardrail layer for all Antigravity-managed agents.

---

## Setup

The rule at `.antigravity/rules/llm-execution-protocol.mdc` is committed.
Antigravity reads it automatically when this repo is loaded.

**In another project:**
```bash
cp .antigravity/rules/llm-execution-protocol.mdc /path/to/your/project/.antigravity/rules/
```

---

## Agent Configuration

```yaml
# .antigravity/agents/coding-agent.yaml
name: coding-agent
model: claude-opus-4
protocol:
  source: .antigravity/rules/llm-execution-protocol.mdc
  mode: strict
  enforcement:
    - no_unrelated_changes
    - no_speculative_code
    - verification_required
```

---

## Mode Override Per Task

```yaml
task:
  id: fix-login-bug-342
  protocol_mode: debug
  description: "Users report 500 error on login with special characters"
```

This overrides the agent's default mode for this task only.

---

## Verification

Antigravity agents log self-evaluation scores after each task.
See `core/self-evaluation.md` for scoring details.
