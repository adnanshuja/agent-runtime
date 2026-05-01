# Integration: Claude Code

Two methods: PLUGIN (global) or CLAUDE.md (per-project).

---

## Method 1: Plugin (Recommended)

**Global install — applies to all projects.**

From within Claude Code:

```
/plugin marketplace add adnanshuja/llm-execution-protocol
/plugin install llm-execution-protocol@llm-execution-protocol
```

The skill `llm-execution-protocol` becomes available. Invoke it:

```
// Tell Claude:
Use LLM Execution Protocol in STRICT mode.
```

Or set as default in `settings.json`:

```json
{
  "skills": {
    "default": ["llm-execution-protocol"]
  }
}
```

### Selecting a Mode

```
Use STRICT mode   → Full protocol, all gates enforced
Use FAST mode     → Minimal protocol, speed optimized
Use DEBUG mode    → Deep investigation, no premature fixes
```

If no mode specified, the system defaults to STRICT.

---

## Method 2: CLAUDE.md (Per-Project)

**For individual projects — copied into the repo.**

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/adnanshuja/llm-execution-protocol/main/CLAUDE.md
```

Or download the full system:

```bash
git clone https://github.com/adnanshuja/llm-execution-protocol.git .llm-protocol
echo "See .llm-protocol/CLAUDE.md for the LLM Execution Protocol system." >> CLAUDE.md
```

---

## Method 3: Skills Directory

**For ~/.claude/skills/ usage.**

Copy the skill definition:

```bash
cp -r skills/llm-execution-protocol ~/.claude/skills/
```

Then reference it as a skill in your CLAUDE.md or settings.

---

## Verification

Confirm the system is active by asking:

```
Run classification on the current task.
```

If the system responds with task type + risk level → active.
If it just answers generically → not loaded.
