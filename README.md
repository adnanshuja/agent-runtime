# Disciplined Agents v3.0.0

**LLMs don't fail because they're dumb. They fail because they lack discipline.**

Hand an LLM a codebase. Watch it rewrite half your files, add abstractions you didn't ask for, and break three things while "fixing" one.

That stops here.

❌ **Default LLM behavior:** Overengineers. Refactors unrelated code. Produces 500-line diffs for 5-line fixes. Hallucinates context. Can't explain why it changed what it changed.

✅ **Disciplined Agents:** Changes only what's required. Justifies every hunk. Rejects unnecessary diffs. Follows a deterministic pipeline. Produces predictable, minimal outputs.

---

## What This Solves

Every pain point of LLM-driven development:

- **Unnecessary refactors** — AI decides to "improve" unrelated code mid-fix
- **Giant diffs** — 50-file changes for a single bug fix
- **Broken logic** — hallucinated dependencies, removed imports, introduced bugs
- **Random assumptions** — "I assumed you wanted X" without asking
- **Untraceable output** — no record of why each change was made
- **Scope creep** — the fix grows beyond the original request

---

## Core Idea

This is **not** a prompt pack. This is not a guidelines document. This is not "add this to your system prompt and hope."

**Disciplined Agents is an execution discipline system** — a deterministic runtime that enforces how an LLM reasons, plans, and produces diffs. Every stage is gated. Every change is justified. Every diff is validated against hard rules.

It turns an LLM from a chaotic autocomplete into a controlled execution engine.

---

## Diff Discipline Engine

This is the main innovation. The thing that separates this from every "prompt framework" on GitHub.

**R1–R8 enforcement rules** that reject bad diffs before they land:

| Rule | Enforces |
|------|----------|
| R1 | Changes stay scoped to the task — no drifting |
| R2 | No speculative additions — if it wasn't asked for, it doesn't go in |
| R3 | Style matches existing code — the LLM doesn't get to rebrand your project |
| R4 | No injected imports, comments, or hidden changes |
| R5 | Verify existing behavior before changing it — measure twice |
| R6 | Hard diff size cap — bloated changes get rejected automatically |
| R7 | Declare dependencies — every change must state what it touches |
| R8 | Rollback protocol — bad diffs are reverted, not fixed forward |

**Every diff hunk requires a justification.** If the LLM can't explain *why* a line changed, the change is rejected.

---

## How It Works

An 8-stage deterministic pipeline, from request to output:

1. **Parse** — classify the request (bug fix, feature, refactor, exploration)
2. **Assumptions** — surface at least 3 implicit assumptions before acting
3. **Interpretations** — generate 2+ interpretations of the request
4. **Decision** — pick the best interpretation and justify
5. **Plan** — explicit step-by-step execution plan
6. **Execution** — per-hunk justified changes only
7. **Verify** — validate against original requirements and diff rules
8. **Trace** — structured output with full audit trail

Each stage has a **hard gate** (H1–H8) that blocks progression if violated.

---

## Real Example

**Request:** *"Fix the off-by-one error in the pagination loop."*

❌ **Default LLM (without discipline):**
- Notices the pagination function needs fixing
- Also decides to "improve" the API response format
- Renames variables for "consistency"
- Adds error handling that doesn't exist yet
- Extracts a helper function nobody asked for
- **Result:** 8 files changed, 200+ lines modified, 1 new bug introduced

✅ **Disciplined Agents (with discipline):**
- Classifies: BUG FIX, LOW risk
- Assumptions: loop boundary, collection size, zero-indexing
- Identifies the exact line
- Changes 1 line: `<=` → `<`
- Justifies: "Iteration exceeds collection length by 1. Changing to strict less-than stops the bounds error. No other callers affected."
- Verifies against tests
- **Result:** 1 file changed, 1 line modified, 0 side effects

This is the difference between asking an LLM to "be careful" and giving it an enforcement system.

---

## Quick Start

**Zero setup. One file. Works everywhere.**

```bash
# Clone or copy one file
git clone https://github.com/adnanshuja/agent-runtime.git
```

Then:

1. Copy `core/execution-engine.md` into your LLM context (Claude Code, Cursor, ChatGPT, Codex — any of them)
2. Say: **"Use Disciplined Agents in STRICT mode"**
3. Tell it what to do

That's it. The engine takes over from there — classification, pipeline, gates, diff discipline, trace.

Cursor users: copy `.cursor/rules/llm-execution-protocol.mdc` into your project for native rule integration.

---

## Structure

```
core/execution-engine.md        <-- start here (single entry point)
core/pipeline.md                S0-S7 execution stages
core/gates.md                   H1-H8 hard constraints
enforcement/diff-discipline.md  R1-R8 diff enforcement
enforcement/per-change-justification.md
enforcement/rejection-protocol.md
context/classifier.md           type + risk + heuristics
modes/                          STRICT | FAST | DEBUG
trace/                          structured output + schema
examples/                       bug-fix, refactor, diff gallery
integrations/                   claude, cursor, chatgpt, vscode
```

---

## Why It Works

Most "prompt engineering" fails because it's advisory. You ask the LLM to "please write clean code" and it does whatever it wants.

Disciplined Agents works because it's **enforced**:

- **Pipeline stages** — the LLM can't skip reasoning steps. It must parse, assume, interpret, decide, plan, then execute. In that order.
- **Hard gates** — each stage has pass/fail criteria. No gate, no progression.
- **Diff discipline** — changes are validated against R1–R8 before landing. Bloated diffs, unjustified changes, and scope creep are rejected automatically.
- **Trace output** — every session produces a structured record. You know exactly what happened and why.

It replaces "hope" with enforcement.

---

**Stop letting AI rewrite your codebase. Start controlling it.**

<p align="center">MIT Licensed</p>
