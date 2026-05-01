# Bad vs. Good: LLM Execution Protocol Applied

Each example shows the SAME user request handled WITHOUT the protocol and WITH the protocol.
Diffs show the measurable difference.

---

## Example 1: "Add caching to the API endpoint"

### User Request

> "The /users endpoint is slow. Add caching."

### ❌ WITHOUT Protocol (Standard LLM Behavior)

**ASSUMPTIONS:** *(none stated — LLM proceeds directly to code)*

```python
# === file: src/api/users.py ===
from functools import lru_cache
import aiocache
from aiocache import cached

@cached(ttl=300, serializer=aiocache.JSONSerializer())
async def get_users():
    """Get all users with caching."""
    cache_key = "users:all"
    
    # Try cache first
    cached = await aiocache.get(cache_key)
    if cached:
        return cached
    
    # Query database
    users = await db.fetch("SELECT * FROM users")
    
    # Cache result
    await aiocache.set(cache_key, users, ttl=300)
    
    return users
```

**Problems:**
- Added `aiocache` dependency without asking
- Implemented async pattern when codebase is synchronous
- Wrapped entire function in caching — no invalidation strategy
- No plan. No assumptions surfaced. No verification.
- Added serializer and TTL that may not match infra

**Diff size:** +35 lines, 1 file, 1 new dependency

### ✅ WITH Protocol (This System)

**STAGE 1 — ASSUMPTIONS:**
```
ASSUMPTIONS:
- "Slow" means >500ms response time (not measured, need to confirm)
- User wants to cache database results, not CDN or HTTP caching
- Cache invalidation strategy is needed (stale data risk)
- The codebase uses synchronous Python (need to verify)
- Redis/memcached is available or in-memory is acceptable
```

**STAGE 2 — INTERPRETATIONS:**
```
1. In-memory LRU cache (simplest, best for single-process)
   - Key difference: No external dependency. Lost on restart.
   - If wrong: Won't help if multiple app servers.
   
2. Redis cache (multi-server, production-grade)
   - Key difference: Requires Redis setup + dependency.
   - If wrong: Overengineered for single-server deployment.

3. Database query optimization (no cache needed)
   - Key difference: Solves root cause instead of symptom.
   - If wrong: The query may already be optimized.
```

**STAGE 3 — DECISION:**
```
DECISION: Start with query optimization + in-memory cache
JUSTIFICATION:
- In-memory cache (lru_cache) is built-in, no deps, reversible
- Addressing root cause first (query perf) defers caching complexity
- Can migrate to Redis later if needed
REJECTED: aiocache (async dep on sync codebase), Redis (premature)
CONFIDENCE: MEDIUM — need to confirm if multi-server deployment
```

**STAGE 4 — PLAN:**
```
PLAN:
1. Profile current endpoint: measure avg response time
   → VERIFY: Baseline measurement recorded

2. Check if query can be optimized (missing index? N+1?)
   → VERIFY: Query plan reviewed, index added if needed

3. If still slow: add @lru_cache with maxsize=128, ttl=300
   → VERIFY: Benchmark shows improvement without stale data

4. Verify no regression: existing tests pass
   → VERIFY: pytest green

BACKOUT: git checkout -- src/api/users.py
```

**STAGE 5 — IMPLEMENTATION:** *(only the needed lines)*

```python
# === file: src/api/users.py ===
from functools import lru_cache

@lru_cache(maxsize=128)
def get_users():
    return db.fetch("SELECT * FROM users")  # unchanged
```

If multi-server needed later, extract to Redis. Not now.

**STAGE 6 — VERIFICATION:**
```
VERIFICATION:
- Tests: 14/14 pass (3 new: cache hit, cache miss, cache invalidation)
- Linters: flake8 clean
- Unrelated changes: none
- Baseline: 850ms → After: 12ms (cached) / 820ms (uncached with index)
```

**Diff size:** +4 lines, 1 file, 0 new dependencies

---

## Example 2: "Fix the form validation bug"

### User Request

> "The registration form doesn't show error messages when the email is invalid."

### ❌ WITHOUT Protocol

```diff
- def validate_registration(form_data):
-     errors = {}
-     if not form_data.get('email'):
+ def validate_registration(form_data: dict) -> dict:
+     """Validate registration form data."""
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

**Problems:**
- Added type hints (style drift)
- Added docstring (style drift)
- Added password validation (not requested)
- Added name validation (not requested)
- Changed logic structure beyond the fix

### ✅ WITH Protocol

**STAGE 1 — ASSUMPTIONS:**
```
ASSUMPTIONS:
- The form submits but errors don't display (not that validation is wrong)
- The error object structure is correct, the display layer is the issue
- The bug is server-side, not client-side JS
```

**STAGE 2 — INTERPRETATIONS:**
```
1. Validation function doesn't detect invalid email
   - Check: Does `@` check catch all invalid cases?
   
2. Validation returns errors but frontend ignores them
   - Check: What does the API response look like?
   
3. The form submits before validation runs
   - Check: Is validation called at all?
```

**STAGE 3 — DECISION:**
```
DECISION: Validate input catches the issue, but error response format
doesn't match what the frontend expects
JUSTIFICATION: "doesn't show error messages" — the validation itself
likely works, the response format/status code is wrong
CONFIDENCE: MEDIUM — need to check the API contract
```

**STAGE 4 — PLAN:**
```
PLAN:
1. Write test: submit invalid email → expect 422 with error details
   → VERIFY: Test fails (current behavior returns what?)

2. Check validation function + response handler for mismatch
   → VERIFY: Root cause identified

3. Fix response format to match frontend expectation
   → VERIFY: Test passes

4. Confirm no regression in valid submission flow
   → VERIFY: Success test still passes

BACKOUT: git diff to review changed lines
```

**STAGE 5 — IMPLEMENTATION:** *(surgical)*

```diff
  def validate_registration(form_data):
      errors = {}
      if not form_data.get('email'):
          errors['email'] = 'Email is required'
-     if '@' not in form_data['email']:
+     elif '@' not in form_data.get('email', ''):
          errors['email'] = 'Invalid email format'
+     
+     # Response handler sends errors with wrong status code
+     # Fix: return 422 instead of 200 when errors present
+     if errors:
+         return jsonify({'errors': errors, 'status': 'error'}), 422
+     
      return errors
```

**Diff size:** +6 lines, 1 file

**STAGE 6 — VERIFICATION:**
```
VERIFICATION:
- Reproduction test: invalid email → 422 + error messages (PASS)
- Regression: valid form → 200 + success (PASS)
- Existing auth tests: 23/23 pass
- Unrelated changes: none
```

---

## Summary: Protocol Impact

| Metric | Without Protocol | With Protocol |
|--------|----------------|---------------|
| Lines changed | 18 | 6 |
| Files changed | 1 | 1 |
| New dependencies | 1 (aiocache) | 0 |
| Unrelated changes | Yes (pwd/name validation, types) | No |
| Root cause identified | No (guessed) | Yes (verified) |
| Test written first | No | Yes |
| Verification run | No | Yes |
| Revertible | Partial | Yes (atomic) |
