---
name: software-engineering
description: How to make engineering decisions and add dependencies, and which principle and task-specific skills to load. Use when executing any task related to software engineering.
---

# Software Engineering

Deliver code that is easy to review, maintain, and extend, not just code that runs.

## Skills

The skills below hold the engineering guidance. Right after reading this skill, before any other step, invoke the skills that apply to the task as you understand it so far:

- for any task that changes code, `/principle-minimize-reader-load` and `/principle-prove-it-works`;
- every other skill below whose description matches the task.

Invoke others when the work later matches them, in particular when a check fails, before verifying and reporting, and when the user corrects the work or adds a requirement.

### Principles

- `/principle-minimize-reader-load`
- `/principle-model-the-domain`
- `/principle-design-for-the-end-state`
- `/principle-boundary-discipline`
- `/principle-fix-root-causes`
- `/principle-prove-it-works`
- `/principle-enforce-lessons-in-codebase`
- `/principle-document-only-what-code-cannot-say`

### Specific tasks

- `/test-audit`
- `/writing-agent-docs`
- `/vet-benchmark-results`
- `/babysit-pr`
- `/handle-automated-review`
- `/contributing-upstream`

## Decisions

Settle implementation details yourself without asking: naming, local structure, and which of several equivalent approaches to take.

In code, decisions that shape the whole deliverable include public interfaces, data and file formats, new dependencies, and whether to keep compatibility.

## Dependencies

Make full use of the ecosystem's existing libraries and tools instead of re-solving well-solved problems.

When adding a dependency, avoid poorly maintained packages or ones with almost no adoption.

When creating a project or adding a dependency, toolchain, or runtime, use the latest version local settings allow, resolved by the package manager or found by a web search, never from memory, which is likely outdated.
