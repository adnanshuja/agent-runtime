# Diff Comparison Gallery: Clean vs Contaminated

The clearest signal of execution quality is the diff. A clean diff touches only what was asked. A contaminated diff carries speculative "improvements."

Use these comparisons as a reference for what passes and fails R1-R8 enforcement.

---

## Example 1: "Add email field to user profile"

### Contaminated (Standard LLM)

```diff
--- a/src/models/user.py
+++ b/src/models/user.py
@@ -1,12 +1,14 @@
 class User:
-    def __init__(self, name, age):
+    def __init__(self, name, age, email=None):
         self.name = name
         self.age = age
+        self.email = email
+        self._normalized_email = None
    
     def greet(self):
         return f"Hi, I'm {self.name}"
    
-    def is_adult(self):
-        return self.age >= 18
+    def is_adult(self, min_age=18):
+        return self.age >= min_age
```

**Contamination:** Changed `is_adult` signature (unrelated). Added `_normalized_email` (speculative). Task was "add email field" — nothing else.

**Violations:** R1, R2

### Clean (Disciplined Agents)

```diff
--- a/src/models/user.py
+++ b/src/models/user.py
@@ -1,7 +1,8 @@
 class User:
-    def __init__(self, name, age):
+    def __init__(self, name, age, email=None):
         self.name = name
         self.age = age
+        self.email = email
    
     def greet(self):
         return f"Hi, I'm {self.name}"
```

**Only changed:** Constructor accepts optional email parameter. Stores it. Nothing else.

---

## Example 2: "Fix error handling in data import"

### Contaminated (Standard LLM)

```diff
--- a/src/import.py
+++ b/src/import.py
@@ -1,28 +1,35 @@
-import csv
+import csv, json, logging
 from pathlib import Path

+logger = logging.getLogger(__name__)
+
 def import_data(filepath):
-    with open(filepath, 'r') as f:
-        reader = csv.DictReader(f)
-        for row in reader:
-            process_row(row)
+    """Import data from CSV file."""
+    logger.info(f"Starting import: {filepath}")
+    
+    path = Path(filepath)
+    if not path.exists():
+        logger.error(f"File not found: {filepath}")
+        raise FileNotFoundError(f"Import file missing: {filepath}")
+    
+    with open(path, 'r') as f:
+        try:
+            reader = csv.DictReader(f)
+            for row in reader:
+                process_row(row)
+        except csv.Error as e:
+            logger.exception(f"CSV parse error: {e}")
+            raise
+    
+    logger.info(f"Import complete: {filepath}")
```

**Contamination:** Added json import (unused). Added docstring (style drift). Added file existence check (unrelated). Reformatted control flow (unnecessary). Added info logging (not requested).

**Violations:** R1, R2, R3, R6

### Clean (Disciplined Agents)

```diff
--- a/src/import.py
+++ b/src/import.py
@@ -1,8 +1,12 @@
 import csv
 from pathlib import Path
+import logging
+
+logger = logging.getLogger(__name__)

 def import_data(filepath):
     try:
         with open(filepath, 'r') as f:
             reader = csv.DictReader(f)
             for row in reader:
                 process_row(row)
+    except FileNotFoundError:
+        logger.error(f"Import file not found: {filepath}")
+        raise
+    except csv.Error:
+        logger.error(f"CSV parsing failed in {filepath}")
+        raise
```

**Only changed:** Wrapped existing body in try/except with logging. Added logger import. Everything else untouched.

---

## Diff Quality Checklist

Use this to evaluate every diff before accepting it:

```
[  ] Every changed line is required by the task
[  ] No files outside the plan were modified
[  ] No type hints added where none existed
[  ] No docstrings added where none existed
[  ] No formatting changes (quotes, spacing, brackets)
[  ] No method signatures changed unless required
[  ] No new dependencies without justification
[  ] No speculative abstractions or patterns
[  ] No commented-out code left behind
[  ] No debug code (prints, console.log) left behind

PASS if all boxes checked. REJECT otherwise.
```
