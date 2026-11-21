---
name: adversarial-review
description: Review a change in rounds, each by a fresh Codex reviewer started through T3 Code, and handle the findings between rounds until the review settles. Use when asked to run an adversarial review, or to conduct one of its rounds as the reviewer.
---

# Adversarial Review

Have the change reviewed by a reviewer that sees only the change and its goal, not how it came about, and repeat until the review finds nothing substantial.

This skill serves two roles. If you were asked to conduct a round as the reviewer, follow only [Reviewing](#reviewing). Otherwise you run the review: follow everything else.

## Before the first round

Start the review after the change achieves its core goal: the feature works, the bug is fixed, the optimization shows. From there the review has one job: bring that change up to quality while keeping it within its scope.

## Loop

Run the review as the maintainer of the change: you answer for the repository and for the change as it will be delivered, and a round's findings are input to that judgment, not a list you must clear.

Start a new reviewer for every round, so the history of earlier rounds can't sway the review: each round tests whether the change stands on its own, as it must for whoever reads it after delivery. That leaves you as the only one who sees across rounds.

Handle a round's findings per `/handle-automated-review`, then verify the fixes. Hold each finding to the priorities the brief states: in a secondary part, reject what doesn't keep that part from doing its job, e.g. edge-case handling in a development tool. Trust that the reviewer's reported commands and outputs occurred as stated instead of rerunning expensive checks just to verify the report, but don't trust its analysis, conclusions, or proposed fixes.

Keep a record in a scratch file outside the repository as you go: each handled finding with the round that raised it, and the size of the change at the start of each round. Build the report from it.

Handling every finding doesn't complete a round. Before starting the next one, judge from the full record whether the change is converging on one that a fresh reviewer has nothing to report on, per "Fix at the scale of the problem" in `/handle-automated-review`. When it isn't, the next step is a [major rework](#major-rework), not another round.

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
- the goal of the change: the outcome its users can observe and the evidence that shows it, not the means the change uses to get there. Add a constraint only when it is part of that outcome: behavior that the program's users or the programs depending on it can see, or a requirement the user stated as a need rather than as a means;
- what matters most: which parts of the change the goal rests on, and which are secondary, e.g. a development tool that comes with the change, so the reviewer spends its attention where a problem costs the most;
- a few pointers into the existing code that we confirmed, e.g. entry points and key call sites: short hints that get the reviewer started fast, neither a complete map nor a description of how the change works;
- the verification already done: that the change achieves its core goal and how we confirmed it, and the current state of the project's checks, e.g. "the full test suite passes", not what was added to get there.

It must leave out earlier findings and how they were handled, and any intermediate state of the rework.

It must also leave out the design: how the change works, the decisions behind it, and the limitations it accepts. The reviewer reads the design from the code and its docs, as every later reader will. A brief that explains or defends the design hides whether they convey it, and confines the reviewer to looking for bugs inside mechanisms it was told to take as given. E.g. state the goal as "importing a 1 GB file stays under 200 MB of memory, shown by the import benchmark", not as "rows are streamed through a bounded queue".

When you want the brief to explain a decision or to call a limitation accepted, the code doesn't convey it yet: change the code or its docs per `/handle-automated-review`, and keep the brief as it was. From one round to the next, the brief should change only where its facts did, such as the scope or the verification results.

### Major rework

When the review is bogged down, more rounds on the same design won't get it out. Step outside the current design and implementation, and think again from the goal: what you would build now, knowing everything the review has shown. Make this call on your own judgment. How the change works is yours to replace without authorization: its mechanisms, its internal structure, and the implementation route taken so far are means, not requirements, even where the change has followed them from the start.

The way out may also cost something visible from outside the program, e.g. a guarantee or a feature its users would lose in exchange for a change that can ship. Consider such options too. A minor one is yours to take, and the report must name it. Before taking a major one, stop and ask the user, reporting as for a review that stopped without settling.

After a major rework, whether it came from your own judgment or from the user changing the requirements:

- confirm the change still achieves its core goal before the next round;
- decide yourself, from how much of the change the rework replaced, whether to start the round budget over.

## Stopping

Stop after handling the latest round's findings when either:

- the review settled;
- the round budget is spent: 3 rounds by default, or 5 when the change touches critical code, such as authentication or a storage format.

The review settled when the latest round raised no blocking finding worth addressing in the parts the goal rests on, and nothing major elsewhere. Fix what minor findings remain without another round. This decision is yours to make without asking the user. Make it rather than spend further rounds polishing secondary parts.

The budget counts only rounds in which the reviewer reviewed the change. A round that didn't, e.g. because the handoff went wrong or the reviewer's thread failed partway, doesn't count: fix the cause and run it again as the same round. A [major rework](#major-rework) may start the budget over.

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

When the review stops without settling, the report must also cover:

- **Where findings concentrated**: which parts of the change drew the most issues and need rework.
- **Why it didn't settle, and alternatives to propose**: why the review kept finding problems, along with concrete options that step outside the current approach for the user to decide on, e.g. dropping a guarantee or feature behavior, or taking a different implementation path.

## Reviewing

Review the change the brief describes against its goal, and reply with your findings. The brief leaves the design out on purpose: read it from the code and its docs.

- Trust the check and verification results the brief reports instead of rerunning them, and run only what confirms a specific finding.
- Spend your attention on the parts the brief says the goal rests on. In a part it names as secondary, report only a problem that keeps that part from doing its job.
- For correctness and performance findings, prove the failure with a run against the real code whenever feasible (e.g. a failing test, reproduction script, or command output showing the wrong behavior or measurement), and report the exact command and output. You may edit any file to reproduce it, but must leave the problem itself unfixed and list the edits in the finding. When a bug cannot be run directly, explain why and give a complete trigger path: the input or state, the steps, and the wrong result.
- Cover aspects including, but not limited to:
  - Correctness: whether the change accurately achieves the original goal.
  - Performance.
  - Quality: whether the code and docs are easy to understand, and whether the change is lean. `/test-audit` sets the bar for tests. A change usually falls short of lean in one of two ways:
    - Unrelated edits: parts, including tests and docs, that could be reverted without changing what the change does.
    - Over-engineering: a simple problem solved in an overly complex way, or a mechanism, feature, or guarantee the goal doesn't need.
- Question whether each mechanism should exist, not only whether it works. No run can prove that one shouldn't, so the evidence for such a finding is: what the goal requires, how the goal is met without the mechanism or with a simpler one, and which code that would delete.
- When several problems trace back to one mechanism, report the mechanism as one finding with the problems as its evidence. A finding per problem invites a patch per problem, each adding to the mechanism.
- Request no change for a speculative risk that doesn't occur under the current code and setup, e.g. one that only matters after a future change or under a different deployment model.
- Treat naming and doc quality problems as blocking like logic bugs, not as polish that can wait because the code works, e.g. inaccurate names, or verbose, unclear, or inconsistent docs.
- Report only blocking findings, and leave out nitpicks and matters of taste. Every reported finding is blocking, so don't grade it with a severity, such as Major/Critical or P0/P1.
- The count has no target: report none rather than pad with nitpicks, and report every blocking finding rather than only the most important few, since anything held back costs another round.
- Report each finding with its aspect, location, problem, evidence, and suggested fix, and nothing else.
