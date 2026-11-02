---
name: principle-model-the-domain
description: Model domain meaning, states, and ownership in data structures. Use before adding state, lifecycles, derived views, or decision records. Also use when choosing core types or sources of truth, or when branches repeat domain rules.
---

# Model the Domain

Get the data shape right before writing logic. A structure that matches the domain makes invalid states unrepresentable and deletes branches, and the code that follows becomes obvious. Choosing it at write time is cheap. Recovering it later looks like a refactor and gets deferred.

Define core types early, trace how each one is read and written, and pick structures that fit the main access paths.

Reach for:

- A state machine instead of scattered booleans, phases, or lifecycle checks.
- A typed model instead of loose parameters or repeated shape assumptions.
- A lookup table, registry, or tagged union instead of branching spread across files.
- A module organized around one body of domain knowledge, not around a sequence such as load, validate, transform, save. Execution order is not ownership.
- The collection the access pattern calls for: a queue, index, tree, or normalized map.
- A type derived from the authoritative schema, when another file already owns the shape, instead of a hand-written parallel type.

When none of these fit, work out what the code must never allow and how the data gets read, then find the structure that encodes exactly that.

Keep plain code when the shape is already clear, local, and unlikely to grow. An abstraction must remove branches, duplicated rules, or invalid states, not just add indirection.

Signs you skipped this step:

- A new feature grows an existing if/else chain by one more branch.
- A second boolean must stay in sync with the first.
- Modules named after phases repeat the same domain rules.
