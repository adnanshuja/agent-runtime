# Integration: Cursor

Use the `.cursor/rules/` file to apply the protocol in Cursor.

---

## Setup

**In this repo:**
The file `.cursor/rules/llm-execution-protocol.mdc` is committed.
Cursor reads it automatically. No extra steps needed.

**In another project:**
Copy the rule file:
```bash
cp .cursor/rules/llm-execution-protocol.mdc /path/to/your/project/.cursor/rules/
```

Then open/refresh Cursor. The rule appears under Settings → Rules.

---

## Mode Selection in Cursor

Add to your `.cursorrules` or tell Cursor in chat:

```
Use LLM Execution Protocol in STRICT mode.
```

Or for quick tasks:

```
Use FAST mode. This is a low-risk typo fix.
```

---

## File: `.cursor/rules/llm-execution-protocol.mdc`

This is the Cursor-specific rule file. It mirrors the core protocol in a format Cursor understands. If you update the core protocol, regenerate this file.

See `.cursor/rules/llm-execution-protocol.mdc`.
