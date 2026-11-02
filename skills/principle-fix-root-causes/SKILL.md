---
name: principle-fix-root-causes
description: Trace each symptom back to the first place the system went wrong and fix it there. Use when debugging, or when fixes that share one assumption keep failing.
---

# Fix Root Causes

Fix the fault, not the place where it surfaced. The symptom is where a wrong state became visible. The root cause is the first place the system did something it should not, or failed to handle a state it should handle.

A symptom fix hides the evidence and leaves the fault free to show up elsewhere. The code then carries a workaround for a bug nobody named.

## Pattern

- Reproduce first.
- Read the actual error and the actual state. When stuck, add instrumentation instead of guessing.
- Ask "why" at each step until the answer is a wrong decision in the code, not a consequence of an earlier one.
- Ask whether the state that triggered the symptom is legitimate. If the system may be in that state, the code that broke on it is the root cause, and handling the state there is the fix. If the state should never exist, a check where it broke hides the bug. Find what produced it.
- Guards, retries, swallowed errors, clamped values, and discarded state are the usual ways to hide a bug.
- When something fails only after a restart, suspect persisted state first: config, caches, lock files, serialized data.
- Fix the pattern, not just the instance. Find every place with the same fault.
- If a workaround needs a paragraph of comment to justify it, the code is wrong.

## Attack the premise

When two fixes sharing one premise have failed the same check, suspect the premise, not the fixes.

1. Write the premise down as one sentence that every failed fix assumed.
2. Find an observation that would come out differently if the premise were false, and make it. Look at how the failures are distributed, not only how large they are.
3. If the evidence contradicts the premise, the cause is elsewhere. If it confirms it, ask the next "why". The earlier fixes may have treated a consequence.

Test the premise before starting the next fix.

## Afterwards

If finding the deciding fact took a deep dig, propose a cheap signal that would have shown it at first look, such as a clearer error message or a log line.
