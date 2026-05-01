# Integration: VS Code

VS Code does not have native agent rules like Cursor or Antigravity.
Use the `.vscode/` directory for task-level configuration.

---

## Setup

The `.vscode/llm-execution-protocol.code-snippets` file provides reusable prompt snippets for VS Code's AI extensions (GitHub Copilot, Cody, etc.).

**In this repo:**
The protocol snippets are at `.vscode/llm-execution-protocol.code-snippets`.

---

## Using with GitHub Copilot

Add the protocol as a system prompt in your VS Code settings:

```json
// .vscode/settings.json
{
  "github.copilot.chat.codeGeneration.instructions": [
    {
      "text": "Follow the LLM Execution Protocol. See .vscode/instructions.md for details."
    }
  ]
}
```

## Using with Cody (Sourcegraph)

In `.vscode/settings.json`:
```json
{
  "cody.chat.preInstruction": "Follow the LLM Execution Protocol. See .vscode/instructions.md"
}
```

---

## Mode Selection

Prefix your chat message with the mode:

```
[STRICT MODE] Add validation to the login form
[FAST MODE] Fix the typo in the README
[DEBUG MODE] The API returns 500 errors intermittently
```
