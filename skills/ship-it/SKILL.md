---
name: ship-it
description: Take a change all the way to a PR squash-merged into the default branch, with independent review and CI handled on the way. Use when the user asks to ship, land, or deliver a change, or when the deliverable is a merged PR.
---

# Ship It

Deliver the change autonomously. By default, carry it until its PR is squash-merged into the default branch.

## Review

CI usually runs automated review on the PR. Run `/adversarial-review` before opening the PR only when the change is complex enough that it should already carry high confidence when pushed, e.g. a large effective diff or intricate lifecycle handling. If it spent its round budget while the last round still found problems worth fixing, the change hasn't reached the quality bar: stop and report, instead of continuing for the steps below.

## PR

Run `/babysit-pr` until its loop ends.

## Merge

Squash-merge when CI passes on the latest push and every actionable item is handled. The squash commit should describe the final change. Respect branch protection and required reviews, without bypasses such as `gh pr merge --admin`.

Stop before merging and report when:

- `/babysit-pr` ended on a human maintainer's comment;
- the repository isn't ours: follow `/contributing-upstream` and stop at the PR.

## Report

- The PR link and the merge commit, or where it stopped and why.
- The reports of `/adversarial-review`, if it ran, and `/babysit-pr`, built from the records they kept.
