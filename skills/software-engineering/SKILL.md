---
name: software-engineering
description: Core software engineering principles and task-specific skills to load. Use when executing any task related to software engineering.
---

# Software Engineering

Deliver code that is easy to review, maintain, and extend, not just code that runs.

## Specific tasks

Invoke these skills when the work matches them:

- `/test-audit`
- `/writing-agent-docs`
- `/vet-benchmark-results`
- `/babysit-pr`
- `/handle-automated-review`
- `/contributing-upstream`

## Decisions

Settle implementation details yourself without asking: naming, local structure, and which of several equivalent approaches to take.

When a fork can be settled by observation (behavior, timing, layout, output, performance), run a small experiment or throwaway prototype and decide or recommend from what you observed. Reserve questions for product or preference tradeoffs no experiment can settle.

In code, decisions that shape the whole deliverable include public interfaces, data and file formats, new dependencies, and whether to keep compatibility.

## Dependencies

Make full use of the ecosystem's existing libraries and tools instead of re-solving well-solved problems. Avoid poorly maintained packages or ones with almost no adoption.

When creating a project or adding a dependency, toolchain, or runtime, resolve the latest version local settings allow via the package manager or a web search, never from memory.

## Principles

### Minimize reader load

Maintainability depends on how much a reader has to trace and hold in their head: the layers between a question and its answer, and the hidden or mutable state in play. Fewer lines or files is not simplicity; fewer abstractions and simpler control flow are.

- Reuse or extend existing code instead of writing a duplicate.
- Inline pass-through layers that add indirection without changing the abstraction, such as single-caller wrappers or single-implementation adapters. Give each module one job behind a narrow interface.
- Give each piece of state one owner and derive the rest. Keep state scope narrow: return values over mutation, locals over fields, fields over module state, module state over globals.
- Enforce an invariant once at its boundary instead of checking it in every consumer.
- Don't pack code to save lines. Use blank lines to separate steps, keep one main operation per line, split long chains and conditions into named intermediate results, and use early returns instead of nesting conditionals, closures, or ternaries inside expressions.
- Using more than one ternary in an expression is forbidden; rewrite it with `if` statements or named steps.
- Prefer plain loops and branches over clever tricks, and keep three similar plain statements over a premature abstraction.
- Clean up the code you touch (rename, extract, inline, delete dead code) while keeping behavior unchanged.

Add a layer or a piece of state only if it removes at least as much complexity elsewhere. If a new reader can't quickly answer where a value comes from and what can change it, cut layers or state.

### Model the domain

Encode the domain in a data structure before writing logic. A structure that matches the domain makes invalid states unrepresentable and deletes branches. Define core types early and fit them to the main access paths.

Reach for a state machine instead of scattered booleans or phase checks; a typed model instead of loose parameters; a lookup table, registry, or tagged union instead of branching spread across files; a module organized around one body of domain knowledge rather than an execution sequence (load, validate, transform, save); the collection the access pattern calls for (queue, index, tree, normalized map); and types derived from the authoritative schema instead of parallel hand-written ones.

Keep plain code when the shape is clear, local, and unlikely to grow. Distrust any abstraction that adds indirection without removing branches, duplicated rules, or invalid states.

Signs you skipped this: a new feature adds another branch to an `if`/`else` chain, a second boolean must stay in sync with the first, or phase-named modules repeat the same domain rules.

### Design for the end state

Design each change as if its requirement had existed from the start, then move the code there instead of bolting it on. Read the affected code first, ask what you would build from scratch, and deliver the redesign in steps that each end in a working state.

- **Clean up unshipped work aggressively**: Treat uncommitted or unmerged changes as unfinished, with no reason to preserve their current shape. After mid-task steering or rework (such as handling review feedback), step back and redesign for the end state again, deleting implementations, parameters, and abstractions that are no longer needed rather than layering fixes on top of the earlier attempt.
- **Constraints are tradeoffs**: Treat everything as changeable by default. Compatibility, minimal diffs, and keeping old APIs are cost tradeoffs, binding only when the user or project explicitly states them. Lead with the design you would build from scratch; present a constrained alternative only when it saves substantial cost, stating what it saves and gives up.
- **Subtract before you add**: Delete dead code, unused abstractions, and speculative guards before adding new code. Design for observed or intended usage, not imagined edge cases.
- **Migrate callers, then delete**: When replacing an internal API with no external users, migrate every caller (types, call sites, tests, docs, examples) and delete the old API in the same change. Keep a compatibility layer or alias only when explicitly required. In a large migration, an intermediate step may leave planned, scoped breakage if you state where it is and run full checks once done.

### Boundary discipline

Validate, narrow types, and handle errors where data crosses a system boundary (CLI arguments, config files, environment variables, network, files, external APIs). Inside, trust the types: no re-validation and no redundant null checks.

- Parse raw data into the program's own types and convert foreign errors at the boundary. Express constraints in the type system (non-null types, enums, newtypes); when the same check keeps reappearing, move that guarantee into the type.
- Expose domain concepts across the boundary, not transport, storage, or framework types. Keep business logic in plain functions with no framework or I/O dependencies, and keep the outer shell thin.
- **Errors**: Each layer should handle only failures it can make a decision about, in one place, and pass the rest up. Use retries, fallbacks, defaults, overrides, or rollbacks only where that layer can reliably fix the failure; consolidate duplicate handling into the layer that owns it. Let one-off scripts (such as ops tasks) fail loudly without retries, timeouts, or recovery structures that can leave half-done state.
- **Two tests**: Is this data crossing a boundary right now? If not, the validation is redundant. Can this be a pure function the shell just calls? If yes, extract it.

### Fix root causes

Fix the fault, not where it surfaced. The root cause is the first place the system did something it shouldn't, or failed to handle a state it should.

- Reproduce first. Ask "why" until the answer is a wrong decision in the code. When stuck, add instrumentation and read the actual error and state instead of guessing.
- Ask whether the triggering state is legitimate. If it is, handle it where the code broke. If it should never exist, a check there hides the bug: find what produced it. Guards, retries, swallowed errors, clamped values, and discarded state are the usual ways to hide a bug. When something fails only after a restart, suspect persisted state first.
- Fix the pattern across every place with the same fault, not just the instance. If a workaround needs a paragraph of comment to justify it, the code is wrong.
- **Attack the premise**: When two fixes sharing one premise fail the same check, suspect the premise. Write it down in one sentence, make an observation that would differ if it were false (checking how failures are distributed, not only their size), and test it before starting the next fix.
- **Afterwards**: If finding the deciding fact took a deep dig, propose a cheap signal that would have shown it at first look, such as a clearer error or log line.

### Prove it works

Verify every result against the real thing directly: run the command, call the endpoint, open the page, or read the actual stored value. Proxies (self-reports, cached output, "it compiles") don't count; static checks (format, lint, type-check, tests) are the first feedback loop, not the last.

- When verification fails, suspect the observation method before the system.
- A measured number is a claim too: name what limits it (core, lock, disk, network) from a profile or counters, not a guess. For performance measurements, load `/vet-benchmark-results`.
- Verification must not disturb the user or other agents sharing the environment.
- Report what you verified and how, and name what you couldn't verify when the reader would expect it. Keep one-off verification scripts in the work trail; commit them only when they meet the bar for a lasting check.

### Enforce lessons in the codebase

When a constraint matters or a mistake recurs (even on first occurrence from user correction or review), enforce it in the codebase. Prefer stronger homes first:

1. A type that makes the wrong state unrepresentable.
2. A name or interface that makes misuse unlikely.
3. An assertion or error at the boundary.
4. A lint, test, or CI check.
5. A comment or doc, only when nothing above can express it.

When a fix is structural, use only the structural fix; don't also add an instruction restating it.

- **Bar for a check**: Keep a check only if it fails for a realistic future bug and survives a behavior-preserving refactor (apply `/test-audit` for tests). Checks matching field names, strings, or file contents usually fail both.
- **Scripts for mechanical work**: For a large mechanical edit, do one unit by hand, write a script (`sed`, `ast-grep`, etc.), verify it reproduces the hand edit, and run it. Keep one-off scripts in the work trail; commit scripts or project tasks for repeated procedures.

### Document only what code cannot say

Documentation is optional; a code change that adds no docs is the normal case. Write for someone who knows only the current code, and prefer expressing facts in names, types, error messages, assertions, or tests. Fix surprising behavior in our own code instead of explaining it in prose; reserve surprise comments for things we can't change (dependencies, platforms, protocols).

- **Audience and placement**:
  - Doc comments serve callers: state the contract (behavior, invariants types don't express, failure modes, caller obligations) so it stays true if the body is rewritten.
  - Inline comments serve maintainers: explain why this line is this way or what breaks if it changes, never what the next lines do.
  - Prose docs serve someone arriving cold: cover concepts, usage, and cross-file decisions; point to entry points rather than restating code, file lists, flags, or exhaustive enumerations that drift. Load `/writing-agent-docs` for how to write them.
  - Match the scope of existing nearby docs, and give each fact one home per audience (keep pointers elsewhere only for obligations, e.g. "new routes also need approval in infra").
- **Describe the present**: Put history in commit messages and plans in TODOs or issues. Delete change-log prose ("now", "no longer", "replaced", "refactored", "will") and docs for removed abstractions. Keep the rule behind history and drop the story.
- **Keep**: safety justifications, invariant/error/panic contracts, why the obvious approach fails, deliberate decisions, links to issues/specs/upstream bugs or copied upstream snippets, license/attribution notices, doctests, and project doc conventions.
- **Cut**: restated signatures/types, body narration, change logs, commented-out code, completed TODOs, empty openers/step markers (`Note:`, `// Step 2`), unrelated cross-tours, test assert narration (name the case instead), hype/padding (`robust`, `seamless`, `simply`, `just`), and work-log doc files.
- **Scope and review**: Apply these rules only to comments you write, edit, or make outdated; leave other existing comments alone unless revising a prose doc itself (follow `/writing-agent-docs`). Check each touched comment in order (true, needed, about current code, at the fact's home, clear at a glance) and cut on the first failure; shorten by cutting points rather than stripping connectives like "because" or "otherwise".
