# Context Classifier

Every task MUST be classified before execution begins.
Classification determines which mode, which enforcement rules, and how much protocol to apply.

---

## Step 1: Classify Task Type

Determine which category the task falls into:

### BUG FIX

**Trigger:** User reports incorrect behavior, crash, or unexpected output.
**Examples:** "The login button doesn't work", "Sorting breaks with empty list", "500 error when submitting form".

**Behavior adaptation:**
- Start with reproduction test (write test that fails)
- MUST identify root cause before implementing fix
- MUST check for same bug pattern elsewhere (tell don't touch)
- Risk: typically MEDIUM

**Forbidden shortcuts:** Patching symptoms without finding root cause. Changing unrelated code "just in case."

### FEATURE

**Trigger:** User requests new capability or enhancement.
**Examples:** "Add export to CSV", "Add dark mode toggle", "Add search bar".

**Behavior adaptation:**
- MUST define minimum viable scope before coding
- MUST identify existing patterns to match
- Risk: varies by scope

**Forbidden shortcuts:** Building beyond the requested scope. Adding configuration for "future" options.

### REFACTOR

**Trigger:** User wants structural changes without behavior changes.
**Examples:** "Extract this into a utility function", "Rename X to Y", "Split this file".

**Behavior adaptation:**
- BEFORE/AFTER equivalence must be provable (same inputs → same outputs)
- Prefer incremental refactors over big-bang
- Test coverage must exist or be added first
- Risk: typically HIGH (touches existing code)

**Forbidden shortcuts:** Refactoring AND adding features in the same change. Refactoring without test coverage.

### EXPLORATION

**Trigger:** User wants investigation without implementation.
**Examples:** "Find why the API is slow", "Check what X library does", "Trace this data flow".

**Behavior adaptation:**
- No implementation without explicit approval
- Report findings before suggesting code
- Risk: LOW (but can escalate)

**Forbidden shortcuts:** Jumping to implementation during investigation.

---

## Step 2: Classify Risk Level

### LOW risk

**Characteristics:**
- Trivial change (typo, one-line fix, obvious)
- No data loss possible
- Reversible with `git checkout`
- Only touches a single file

**Behavior:**
- FAST mode allowed
- Protocol stages can be abbreviated
- No user confirmation needed between stages

### MEDIUM risk

**Characteristics:**
- Multiple files changed
- Logic changes, not just formatting
- Could affect other features (regression risk)
- Touches shared infrastructure

**Behavior:**
- STRICT mode default (FAST with explicit permission only)
- Full protocol required
- User confirmation required at Stage 3 (Decision)
- Tests MUST pass before and after

### HIGH risk

**Characteristics:**
- Destructive operations (migrations, deletions, renames)
- Data model or database changes
- Security boundaries
- Production configuration
- Dependency changes
- CI/CD changes

**Behavior:**
- STRICT mode REQUIRED. DEBUG mode allowed. FAST mode FORBIDDEN.
- Full protocol. No abbreviation.
- User confirmation required at EVERY gate.
- Verification MUST include rollback testing.
- Automated test suite MUST pass.

---

## Step 3: Behavioral Adaptation Matrix

After classification, apply these rules:

| Task Type | Default Mode | User Confirmation | Protocol Strictness |
|-----------|-------------|-------------------|---------------------|
| BUG FIX (LOW) | FAST | After Stage 1 | Abbreviated |
| BUG FIX (MED) | STRICT | After Stage 3 | Full |
| BUG FIX (HIGH) | STRICT | Every stage | Full + Enforcement |
| FEATURE (LOW) | FAST | After Stage 1 | Abbreviated |
| FEATURE (MED) | STRICT | After Stage 3 | Full |
| FEATURE (HIGH) | STRICT | Every stage | Full + Enforcement |
| REFACTOR (LOW) | FAST | After Stage 4 | Abbreviated |
| REFACTOR (MED) | STRICT | After Stage 4 | Full (Stage 5 heavily gated) |
| REFACTOR (HIGH) | STRICT | Every stage | Full + Enforcement |
| EXPLORATION | FAST | After report | No protocol (investigation only) |

---

## Default Classification

When in doubt:
- **Task type:** BUG FIX (conservative — treat unknowns as bugs)
- **Risk level:** MEDIUM (conservative — underestimate risk at your own cost)
- **Mode:** STRICT (conservative — slow is safe, fast is risky)
