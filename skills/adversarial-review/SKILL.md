---
name: adversarial-review
description: Review a change in rounds, each by a fresh Codex reviewer started through T3 Code, and handle the findings between rounds until the review settles. Use when asked to run an adversarial review, or to conduct one of its rounds as the reviewer.
---

# Adversarial Review

Have the change reviewed by a reviewer that sees only the change and its goal, not how it came about, and repeat until the review finds nothing substantial.

This skill serves two roles. If you were asked to conduct a round as the reviewer, follow only [Reviewing](#reviewing). Otherwise you run the review: follow everything else.

## Loop

Start a new reviewer for every round, so the history of earlier rounds can't sway the review. Handle its findings per `/handle-automated-review`, then verify the fixes before the next round. Trust that the reviewer's reported commands and outputs occurred as stated instead of rerunning expensive checks just to verify the report, but don't trust its analysis, conclusions, or proposed fixes. Record each handled finding, with the round that raised it, in a scratch file outside the repository as you go, and build the report from it.

Commit the work before each round, so the reviewer's edits show up as their own diff. Treat those edits as evidence, not finished code: rework a reproduction into its long-term form when the fix needs it, e.g. a regression test that fits the suite per `/test-audit`, and revert the rest.

### Reviewer

Start it with `delegate_task`, titled with the round number, and wait until it completes:

```json
{
  "target": {
    "providerInstanceId": "codex",
    "model": "gpt-6.2-sol",
    "options": { "reasoningEffort": "high", "serviceTier": "default" }
  },
  "title": "Adversarial Review Round 1",
  "isolation": "shared",
  "mode": "wait"
}
```

If this target is unavailable, stop and report instead of switching to another model.

### Brief

The brief must ask the reviewer to conduct a round per `/adversarial-review`, without restating the skill's requirements.

Beyond that, the brief must contain only:

- the baseline and the scope of the change, e.g. `origin/main...HEAD` plus uncommitted changes;
- the original problem, as the user or the issue stated it;
- evidence from the existing code that we confirmed, e.g. entry points and key call sites, so the reviewer gets started fast;
- the goal of the change;
- important design decisions and constraints, stated as the current design;
- the current state of the project's checks, e.g. "the full test suite passes", not what was added to get there.

It must leave out earlier findings and how they were handled, and any intermediate state of the rework.

## Stopping

Stop after handling the latest round's findings when either:

- the review settled: the latest round reported no findings, or you rejected all of them;
- the round budget is spent: 3 rounds by default, or 5 when the change touches critical code, such as authentication or a storage format.

When the round budget is spent without settling, the change hasn't met the quality bar. Without explicit prior authorization from the user to proceed or merge regardless of the review outcome, you must treat the work as blocked and stop rather than continuing.

## Report

Report why the loop stopped and how many rounds ran, then every finding on one line each, as `Round N / aspect / summary: outcome, what you did`, with N the round that raised it and the outcome from `/handle-automated-review`. E.g.:

```
Settled after 3 rounds: round 3 reported no findings.

- Round 1 / Correctness / `import` overwrites existing rows when two files share an ID: different fix, keyed rows on (file, ID) instead of rejecting duplicate IDs; turned the reviewer's reproduction into a regression test.
- Round 1 / Correctness / `--port` accepts 0: rejected, 0 asks the OS for a free port, which the test harness relies on; documented this in the flag's help text.
- Round 1 / Quality / retry progress tracked in three booleans that drift apart: different fix, redesigned as a `RetryState` enum instead of syncing the flags.
- Round 2 / Quality / `retry_count` also counts the first attempt: as suggested, renamed to `attempt_count`.
```

When the round budget is spent without settling, the report must also cover:

- **Where findings concentrated**: which parts of the change drew the most issues and need rework.
- **Why it didn't settle, and alternatives to propose**: why the review kept finding problems, along with concrete options that step outside the current approach for the user to decide on, e.g. dropping a guarantee or feature behavior, or taking a different implementation path.

## Reviewing

Review the change the brief describes against its goal, and reply with your findings.

- Trust the reported check results instead of rerunning them, and run only what confirms a specific finding.
- For correctness and performance findings, prove the failure with a run against the real code whenever feasible (e.g. a failing test, reproduction script, or command output showing the wrong behavior or measurement), and report the exact command and output. You may edit any file to reproduce it, but must leave the problem itself unfixed and list the edits in the finding. When a bug cannot be run directly, explain why and give a complete trigger path: the input or state, the steps, and the wrong result.
- Cover aspects including, but not limited to:
  - Correctness: whether the change accurately achieves the original goal.
  - Performance.
  - Quality: whether the code and docs are easy to understand, and whether the change is lean. `/test-audit` sets the bar for tests. A change usually falls short of lean in one of two ways:
    - Unrelated edits: parts, including tests and docs, that could be reverted without changing what the change does.
    - Over-engineering: a simple problem solved in an overly complex way.
- Request no change for a speculative risk that doesn't occur under the current code and setup, e.g. one that only matters after a future change or under a different deployment model.
- Treat naming and doc quality problems as blocking like logic bugs, not as polish that can wait because the code works, e.g. inaccurate names, or verbose, unclear, or inconsistent docs.
- Report only blocking findings, and leave out nitpicks and matters of taste. Every reported finding is blocking, so don't grade it with a severity, such as Major/Critical or P0/P1.
- The count has no target: report none rather than pad with nitpicks, and report every blocking finding rather than only the most important few, since anything held back costs another round.
- Report each finding with its aspect, location, problem, evidence, and suggested fix, and nothing else.
