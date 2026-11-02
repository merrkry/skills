---
name: principle-enforce-lessons-in-codebase
description: Encode lasting constraints in types, interfaces, or checks. Use when user corrections or review reveal a rule, even on its first occurrence. Also use when deciding whether code or agent instructions should enforce a rule, or when automating large edits or repeated procedures.
---

# Enforce Lessons in the Codebase

When a constraint matters or a mistake recurs, put it where the code enforces it. Prefer these homes, strongest first:

1. A type that makes the wrong state unrepresentable.
2. A name or interface that makes the wrong use unlikely.
3. An assertion or error at the boundary that would catch it.
4. A lint, test, or CI check.
5. A comment or doc, only when nothing above can express it.

If the fix is structural, use only the structural fix. Don't also add an instruction restating it.

## The bar for a check

Most generated checks pin the current implementation and catch nothing. Keep a check only if:

- it would fail for a realistic future bug, and
- it keeps passing after a refactor that keeps behavior.

Checks that match field names, strings, or file contents usually fail both. For tests, apply `/test-audit`.

## Scripts for mechanical work

For a large mechanical edit, write a script instead of editing many files by hand, e.g. with `sed` or `ast-grep`. Do the first unit by hand, then check the script reproduces it.

Keep such a script in the work trail. When a complex command or procedure runs repeatedly, extract it into a script or a project task and commit it.
