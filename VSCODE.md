# LLM Execution Protocol + VS Code

VS Code does not have a native agent instruction system like Cursor or Antigravity.
Use `.vscode/` directory configuration to integrate with AI extensions.

---

## Setup

### GitHub Copilot

Add to `.vscode/settings.json`:

```json
{
  "github.copilot.chat.codeGeneration.instructions": [
    {
      "text": "Follow the LLM Execution Protocol. See .vscode/llm-execution-protocol.code-snippets for mode selection. Default mode is STRICT."
    }
  ]
}
```

### Cody (Sourcegraph)

Add to `.vscode/settings.json`:

```json
{
  "cody.chat.preInstruction": "Follow the LLM Execution Protocol. Default mode: STRICT."
}
```

### Continue.dev

Add to `.vscode/continue.json`:

```json
{
  "models": [{
    "title": "Claude with Protocol",
    "provider": "anthropic",
    "model": "claude-opus-4",
    "systemMessage": "Follow the LLM Execution Protocol defined in .vscode/llm-execution-protocol.code-snippets. Default mode: STRICT."
  }]
}
```

---

## Mode Selection

Prefix your chat messages:

```
[STRICT MODE] Add validation to the login form
[FAST MODE] Fix the typo in the README
[DEBUG MODE] The API returns 500 errors intermittently on Tuesdays
```

---

## Snippets

The file `.vscode/llm-execution-protocol.code-snippets` provides reusable prompt snippets for quick mode and stage reference within VS Code.
