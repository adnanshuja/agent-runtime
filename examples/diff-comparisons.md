# Diff Comparisons: Clean vs. Contaminated Diffs

The clearest signal of LLM quality is the diff it produces.
A clean diff touches only what was asked. A contaminated diff carries "improvements."

---

## Example 1: "Add an email field to the user profile"

### Contaminated Diff (Standard LLM)

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

**Contamination:** Changed `is_adult` signature (unrelated). Added `_normalized_email` (speculative). Task was "add email field."

### Clean Diff (Protocol Compliant)

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

**Only changed:** Constructor to accept optional email parameter. Nothing else.

---

## Example 2: "Fix error handling in the data import function"

### Contaminated Diff

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

**Contamination:** Added logging. Added json import (unused). Added file existence check. Added docstring. Changed `filepath` to `path`. Reformatted control flow. None of this was requested.

### Clean Diff

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

**Only changed:** Wrapped existing body in try/except with logging. Added import. Everything else unchanged.

---

## Diff Quality Checklist

Use this to evaluate any diff before accepting it:

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
