# Example: Refactor — Disciplined vs Standard LLM

**Scenario:** "The /users endpoint is slow. Add caching."

---

## ❌ Standard LLM Output

No plan. No assumptions. Jumps straight to code.

```python
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

**Violations:**
- **R7 (Hidden dependency):** Added `aiocache` without asking or stating it in the plan
- **R3 (Style drift):** Added async pattern when codebase is synchronous
- **R2 (Speculative generality):** Added serializer, TTL, and full caching architecture without knowing the deployment model
- **No stage progression:** Zero assumptions stated, zero interpretations, zero plan

**Diff size:** +35 lines, 1 file, 1 new dependency

---

## ✅ Disciplined Agents Output

### CLASSIFY
```
Type: REFACTOR (adding caching without changing API behavior)
Risk: HIGH (touches data flow, performance-sensitive, regression risk)
Mode: STRICT
```

### PROTOCOL EXECUTION

**Stage 1 — Assumptions:**
```
ASSUMPTIONS:
- "Slow" means >500ms p50 response time (not measured — need baseline)
- User wants server-side response caching, not CDN or HTTP caching
- Cache invalidation strategy matters (stale data risk exists)
- The codebase uses synchronous Python (need to verify)
- Caching layer decision (in-memory vs Redis) depends on deployment topology
```

**Stage 2 — Interpretations:**
```
1. In-memory LRU cache — simplest, built-in, no deps
   If correct: 0 dependencies, trivial rollback
   If wrong: Won't help with multiple app servers

2. Redis cache — production-grade, works across servers
   If correct: Scales to multi-server deployment
   If wrong: Overengineered, adds ops burden for single-server

3. Database query optimization — fix root cause, not symptom
   If correct: No caching complexity needed at all
   If wrong: Query may already be optimal
```

**Stage 3 — Decision:**
```
DECISION: Approach 1 (measure first → query optimization → in-memory cache)
JUSTIFICATION:
- Cannot optimize without baseline measurement (rule: measure before change)
- Query optimization addresses root cause if N+1 or missing index
- lru_cache is built-in, zero deps, reversible in 2 lines
- Redis can be layered on later if topology demands it
REJECTED: aiocache (async dep on sync codebase = style violation)
REJECTED: Redis (premature without knowing deployment architecture)
CONFIDENCE: MEDIUM — need to confirm query profile
```

**Stage 4 — Plan:**
```
PLAN:
1. Profile current endpoint: measure p50/p95 response time
   → VERIFY: Baseline metric recorded
2. Check query plan: missing index? N+1 pattern? Unnecessary columns?
   → VERIFY: Query optimized or confirmed optimal
3. If still needed: add @lru_cache with maxsize=128
   → VERIFY: Benchmark shows improvement, no stale data
4. Full test suite: confirm no regression
   → VERIFY: All tests pass

BACKOUT: git checkout -- src/api/users.py
```

**Stage 5 — Implementation:**

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def get_users():                         # ← unchanged body
    return db.fetch("SELECT * FROM users")
```

No async. No external dependency. No restructuring. No speculative TTL or serializer.

**Diff size:** +3 lines, 1 file, 0 new dependencies

### ENFORCEMENT CHECK
```
R1 (No unrelated): PASS — only the caching decorator was added
R2 (No speculative): PASS — maxsize=128 is reasonable, no future config
R3 (No style drift): PASS — matches synchronous style of codebase
R6 (Diff size): PASS — 3 lines
R7 (No hidden deps): PASS — functools is standard library
R8 (Rollback): PASS — trivial revert
```

### SCORECARD
```
C: 10 — caching works, no regression, verified with benchmark
M: 10 — 3 lines, 0 deps, exactly what was needed
A: 10 — assumptions correct: sync codebase, query was N+1, index fixed
V: 10 — baseline measured, tests pass, diff discipline clean

SCORE: 10.0 (PASS)
```
