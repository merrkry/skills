---
name: principle-prove-it-works
description: Verify work against the real artifact before declaring it done, without disturbing others. Use after completing a change, before reporting results, or when choosing how to verify.
---

# Prove It Works

Verify every result by checking the real thing. Don't infer it from proxies, self-reports, or "it compiles".

Unverified work has unknown correctness. Acting on a wrong inference costs far more than checking the source.

## Check the real thing

- The project's static checks are the first feedback loop, not the last. Format, lint, type-check, and run tests as the project requires.
- Verify end to end where you can: run the command, call the endpoint, open the page, or read the stored value.
- Read the actual value, not a cached or derived representation of it.
- When verification fails, suspect the observation method before the system.
- A measured number is a claim too. Say what limits it, e.g. a core, a lock, the disk, or the network. If you can't say why it isn't twice as good, you don't know what you measured. For performance work, use `/vet-benchmark-results`.

Verification follows the same rule as any other work: don't block the user or other agents sharing the environment.

## Report

State what you verified and how. Mention what you couldn't verify when the reader would expect it verified. Keep a one-off verification script in the work trail. Commit it only when it becomes a long-term check, per `/principle-enforce-lessons-in-codebase`.
