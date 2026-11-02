---
name: principle-minimize-reader-load
description: Keep code simple by minimizing what a reader must trace and hold in their head. Use when writing, reviewing, or refactoring code, or when tempted to add a layer, wrapper, or piece of state.
---

# Minimize Reader Load

Maintainability is the work a reader must do to understand code. It has two independent axes:

- Layers to trace: indirections between a question and its answer.
- State to hold: hidden or mutable context the reader must keep in mind.

Simplicity means fewer abstractions and simpler control flow, not fewer lines or files. A flat file with 50 globals can be as hard to follow as a six-layer adapter stack.

## Layers

- Reuse or extend existing code instead of implementing the same thing again.
- Collapse layers that cost more than they save: wrappers with one caller, adapters with one implementation, indirection added for a need that never came.
- Each layer should change the abstraction. A layer that repeats the methods and arguments of the one below is a pass-through. Inline it.
- Prefer narrow interfaces that hide real decisions over broad ones that hide little.
- Give each module one job, and hide its details behind a clear interface.

## State

- Give each piece of state or data one owner. Derive the rest from it instead of keeping copies in sync.
- Shrink state scope: return values over mutation, locals over fields, fields over module state, module state over globals.
- State an invariant once at the boundary that enforces it, not in every consumer.

## Code

Use blank lines to split code into steps, and name intermediate results.

Line count is not reader load. Every operation on a line and every level of nesting is something the reader must hold at once. You must not pack code densely to save lines:

- Keep one main operation per line. Split long chains of calls, conditions, or arithmetic, and name the intermediate results.
- Don't nest conditionals, ternaries, or closures inside expressions. Use early returns and named steps.
- Using more than one ternary in an expression is forbidden. Rewrite it with `if` statements or named intermediate results.
- Don't use clever tricks where a plain loop or branch is clearer.

Three similar plain statements beat a premature abstraction.

Make micro-refactors aggressively in the areas you touch: renaming, extracting, inlining, and deleting dead code, as long as behavior stays the same.

## The test

Before adding a layer or a piece of state, ask whether it reduces reader load elsewhere by at least as much.

Can a new reader answer "where does X come from?" and "what can change X?" quickly? If not, cut layers or cut state.
