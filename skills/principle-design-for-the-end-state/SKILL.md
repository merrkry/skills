---
name: principle-design-for-the-end-state
description: Integrate changes by redesigning towards the correct end state instead of bolting them on. Use when adding a requirement to an existing design, replacing an internal API, or planning a refactor, rewrite, or migration.
---

# Design for the End State

Design each change as if its requirement had existed from the start, then move the code there, instead of bolting it onto the existing design.

- Read the affected code and understand the current design.
- Ask: "if we wrote this from scratch with this requirement, what would we build?"
- Think through the whole redesign, then deliver it in steps that should each end in a working state.

## Constraints are tradeoffs

Treat everything as changeable by default. Compatibility, a minimal diff, keeping an old API, and avoiding breakage are constraints: tradeoffs that save cost, such as pushing changes to external users or migrating existing code.

Start from first principles and make the design you would build from scratch the first choice. When following a constraint or accepting a less ideal design cuts the cost significantly, present it as an alternative, with what it saves and what it gives up.

Treat only constraints the user or the project states explicitly as binding, e.g. a published library with a compatibility promise.

## Subtract before you add

Remove complexity first, then build on the simpler base.

- Delete dead code, unused abstractions, and speculative guards before adding new code.
- Design for observed or intended usage, not imagined edge cases.
- Leave the design simpler and more capable behind the same or a smaller surface.

## Migrate callers, then delete

When replacing an internal API with no external users, migrate every caller and delete the old API in the same change. Keep a compatibility layer, an alias, or a deprecated path only when the user or the project explicitly requires it.

Propagate the change through every reference: types, call sites, tests, docs, and examples. Update tests to the new contract, and delete tests that only protected the old one.

In a large migration, an intermediate step may leave breakage when it is planned and scoped. Say where it is, and run the full checks when the migration is done.
