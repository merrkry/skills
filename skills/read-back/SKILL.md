---
name: read-back
description: Read back a plan, the findings of an investigation or experiment, or a finished change, so the user can confirm we share the same understanding and decisions. Use when the user asks for a read-back before the work moves on.
disable-model-invocation: true
---

# Read-back

Give a read-back, like a pilot reading back a clearance. The user compares it with their understanding, so any mismatch surfaces before the work moves on.

Pick the kind that matches where the task stands:

- Plan: before a change, covering the change we intend to make.
- Findings: after an investigation or experiment, covering what we learned and the solution it points to.
- Report: after a change is made, covering what changed and how it was verified.

Build the read-back from the original problem, as the user stated it or as it follows from the context, and answer it with the current state of knowledge and code. Tell the user where things stand, not how we got here: report a result by what it shows, and mention an attempt or a dead end only when it bears on a conclusion or decision.

Keep the read-back to what was established in the conversation, verified in code, or done during the work.

## Read-back

Skip any section or bullet below that doesn't apply to the task or the kind. Keep each section to a few bullets. Cite code by file and line number, or by exact symbol.

### Background

Keep facts, goals, and analysis in separate layers:

- Original problem: the factual sources, such as the issue, error message, failing command, or user report, quoted or linked as-is.
- Expected behavior: what the user or caller should observe once it works, not how it is implemented.
- Verified: what was confirmed by action, such as reproductions, experiments, debugging, or a trial implementation. State what each one showed, with just enough of the method to judge the evidence.
- Analysis: conclusions drawn from reading code, docs, or history, and the root cause they point to. Summarize the reasoning, not every file read, and cite the few key places needed to verify the analysis or rebuild the change.
- Conclusion, in findings: the answer to the question that started the investigation, how firmly the evidence supports it, and the hypotheses it ruled out.

### Decisions

A decision is a design point that shapes the whole deliverable and is one of:

- A constraint: something we deliberately keep unchanged or consistent because of engineering concerns, such as complexity or compatibility.
- A choice the conversation constrained, reworked, or debated.
- A tradeoff, where some behavior ends up short of the expected behavior, common practice, or consistency with similar code in the project.

In a report, also include decisions made on the user's behalf during the work, and every departure from the confirmed plan.

Leave implementation details out of the read-back by default. If one changes visible behavior, describe it under Boundary behavior. For each decision:

- What was chosen, and the alternatives considered.
- Why, citing the user instruction or evidence behind it.
- The cost accepted.

### Change

The change we propose in a plan or findings, or the change made in a report. When the findings leave the root cause open, propose the next experiment instead, and what it would settle.

- High-level description: what changes and at which layer, in a few sentences, not a diff.
- Unchanged behavior: externally visible behavior the design keeps as-is, and the layer at which it holds.
- Boundary behavior: edge cases, error paths, invalid or empty input, and how behavior differs before and after.
- Risk: what could break, what is affected (callers, data, running services), and how we would notice.
- Verification, in a report: what was run to check the change, such as tests, builds, or a reproduction of the original problem, and the results. Name what remains unchecked and why.

### Blockers

A blocker is where we still need a decision from the user. It has several viable options, and picking one is a tradeoff only the user can weigh. If one option is clearly better, it is an implementation detail, not a blocker. For each blocker, list the options and what each one costs.

## Visualization

For complex changes, add visualizations wherever it explains something better than prose, such as structure, interactions, data flow, or before/after behavior. Prefer an HTML render tool over Mermaid or ASCII art.

## Completion

End the turn and wait for the user's reply. Act on a proposed change or next experiment only when the read-back has no blockers and the user confirms it.

If the read-back had blockers, or the user asks for rework, give a new read-back of only the parts that changed, sized to the rework. Skip visuals when the change is small and clear. Give a full read-back when the user invokes this skill again or asks for one.
