---
name: software-engineering
description: How to make engineering decisions and add dependencies, with an index of the principle and task-specific skills to load. Use when executing any task related to software engineering.
---

# Software Engineering

Deliver code that is easy to review, maintain, and extend, not just code that runs.

## Skills

Load each skill below when it is relevant to the task.

### Principles

- `/principle-minimize-reader-load`: simplicity means fewer layers to trace and less state to hold, not fewer lines. Reuse existing code, keep one source of truth, collapse layers that don't pay for themselves, and never pack code densely to save lines.
- `/principle-model-the-domain`: get the data shape right before writing logic. Encode the domain in a structure, such as a state machine, a typed model, or a table, instead of scattered conditionals.
- `/principle-design-for-the-end-state`: build as if the new requirement had existed from the start. Remove dead code first, migrate callers instead of keeping compatibility layers, and treat constraints as cost tradeoffs that bind only when stated explicitly.
- `/principle-boundary-discipline`: validate and convert external input at system boundaries, and trust what the boundary guarantees inside. Each layer handles only the failures it owns, with rigor matched to how long the code will live.
- `/principle-fix-root-causes`: fix the first place the system went wrong, not where the symptom showed up. When fixes sharing one premise keep failing, test the premise.
- `/principle-prove-it-works`: verify against the real artifact, not a proxy such as "it compiles".
- `/principle-enforce-lessons-in-codebase`: turn recurring lessons into types, names, assertions, checks, and scripts rather than prose, but only when the check would catch a realistic future bug.
- `/principle-document-only-what-code-cannot-say`: docs are optional. A comment or doc earns its place only with a point the reader can't get from the code, at the scope of the docs around it. Describe the present, and leave history to the work trail.

### Specific tasks

- `/test-audit`: when adding, changing, reviewing, or deleting tests, or changing code covered by tests.
- `/writing-agent-docs`: when writing or editing prose docs, such as READMEs, AGENTS.md, and CONTRIBUTING, or skills and prompts for other agents.
- `/vet-benchmark-results`: when running a benchmark, comparing the performance of options, or reporting a speedup or regression you measured.
- `/babysit-pr`: when a PR needs to get through CI and review.
- `/handle-automated-review`: when handling findings from review bots or other agents.
- `/contributing-upstream`: when opening a discussion, issue, or pull request on a project we don't own.

## Decisions

Settle implementation details yourself without asking: naming, local structure, and which of several equivalent approaches to take.

In code, decisions that shape the whole deliverable include public interfaces, data and file formats, new dependencies, and whether to keep compatibility.

## Dependencies

Make full use of the ecosystem's existing libraries and tools instead of re-solving well-solved problems.

When adding a dependency, avoid poorly maintained packages or ones with almost no adoption.

Add dependencies through the package manager so it resolves the latest eligible version that satisfies local minimum release age settings, e.g. `pnpm add` or `cargo add`.
