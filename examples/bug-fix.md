# Example: Bug Fix — Disciplined vs Standard LLM

**Scenario:** "The registration form doesn't show error messages when the email is invalid."

---

## ❌ Standard LLM Output

The LLM immediately writes code without classification, protocol, or enforcement.

```diff
- def validate_registration(form_data):
-     errors = {}
-     if not form_data.get('email'):
+ def validate_registration(form_data: dict) -> dict:
+     """Validate registration form data with comprehensive checks."""
+     errors: dict = {}
+     
+     email = form_data.get('email', '')
+     if not email:
          errors['email'] = 'Email is required'
-     if '@' not in form_data['email']:
+     elif '@' not in email:
          errors['email'] = 'Invalid email format'
     
+     if not form_data.get('password'):
+         errors['password'] = 'Password is required'
+     if len(form_data.get('password', '')) < 8:
+         errors['password'] = 'Password too short'
+     
+     if not form_data.get('name'):
+         errors['name'] = 'Name is required'
+     
      return errors
```

**Violations:**
- **R3 (Style drift):** Added type hints, docstring — file had neither
- **R1 (Unrelated changes):** Added password validation (not requested)
- **R1 (Unrelated changes):** Added name validation (not requested)
- **R2 (Speculative generality):** Restructured logic for "future" extensibility

**Diff size:** +18 lines, 5 unnecessary changes

---

## ✅ Disciplined Agents Output

### CLASSIFY
```
Type: BUG FIX
Risk: MEDIUM (touches form submission flow, regression possible)
Mode: STRICT
```

### PROTOCOL EXECUTION

**Stage 1 — Assumptions:**
```
ASSUMPTIONS:
- The form submits but validation errors don't display (validation is running)
- The error object structure is correct, but the response format is wrong
- The bug is server-side, not client-side JS
- The validation function itself works for detecting invalid emails
```

**Stage 2 — Interpretations:**
```
1. Validation detects the error but response format doesn't match frontend expectation
   If correct: Fix is in the response handler, not validation logic
   If wrong: Wasted time checking API contract

2. Validation logic doesn't run before form submission
   If correct: Fix is in the submission flow
   If wrong: Wasted time tracing execution path

3. Email validation check has a logic bug
   If correct: Fix is in the condition itself
   If wrong: Would fix a symptom not the cause
```

**Stage 3 — Decision:**
```
DECISION: Interpretation 1 (response format mismatch)
JUSTIFICATION: "doesn't show error messages" implies validation runs but result is ignored/not displayed
REJECTED: Interpretation 2 (form submits, it just shows no errors)
REJECTED: Interpretation 3 (email check works, the display is the problem)
CONFIDENCE: MEDIUM — need to verify API contract
```

**Stage 4 — Plan:**
```
PLAN:
1. Write reproduction test: submit invalid email → observe response
   → VERIFY: Current response captured
2. Compare response structure against frontend expectation
   → VERIFY: Root cause identified (mismatch in status code or error format)
3. Fix response handler to return 422 with structured error body
   → VERIFY: Test passes with correct error display
4. Confirm no regression in valid-submission flow
   → VERIFY: Success test still passes

BACKOUT: git checkout -- src/handlers/registration.py
```

**Stage 5 — Implementation (surgical fix):**

```diff
  def validate_registration(form_data):
      errors = {}
      if not form_data.get('email'):
          errors['email'] = 'Email is required'
-     if '@' not in form_data['email']:
+     elif '@' not in form_data.get('email', ''):
          errors['email'] = 'Invalid email format'
+     
+     # Bug: response sends 200 with errors → frontend only reads 422
+     if errors:
+         return jsonify(errors), 422
+     
      return errors
```

No type hints. No docstring. No unrelated validations. No restructuring.

**Diff size:** +6 lines, 1 file, 0 new dependencies

### ENFORCEMENT CHECK
```
R1 (No unrelated): PASS — all lines serve the fix
R2 (No speculative): PASS — no future parameters or abstractions
R3 (No style drift): PASS — matches file conventions exactly
R6 (Diff size): PASS — 6 lines, well under 50-line cap
```

### SCORECARD
```
C: 10 — bug fixed, all tests pass, edge cases handled
M: 10 — every line necessary, zero waste
A: 9 — assumptions verified, one minor environment detail wrong
V: 10 — reproduction test, regression test, linting, diff check

SCORE: 9.75 (PASS)
```
