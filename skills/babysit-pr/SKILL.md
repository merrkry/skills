---
name: babysit-pr
description: Watch a PR's CI and reviews, and work through failures and review comments in rounds until it settles. Use when a PR needs to get through CI and review, e.g. after opening or pushing to one.
---

# Babysit PR

Watch the PR and work through its CI failures and review comments in rounds. In each round, handle every actionable item that has arrived, push once, then wait for the next event.

## Watch

If the work has no commit or PR yet, commit it and open a PR first.

Watch CI and comments with the tools available. Prefer an event-driven tool that wakes you on new checks or comments. Without one, poll every 250 seconds.

## Each round

Handle every actionable item that has arrived: failed checks, unresolved review threads, and actionable feedback in top-level comments and review bodies. Record each handled item in a scratch file outside the repository as you go, and build the report from it.

Handle review comments by author:

- **Bot**: handle it fully per `/handle-automated-review`, including replies and resolving.
- **Human maintainer**: keep the whole round local for the user to review, and end the loop.

### Push

Rebuild the PR history and force push with `--force-with-lease`. Fold each fix into the commit it belongs to, so the PR reads as if written right the first time and every round leaves a clean history.

Once the push succeeds, post the round's replies and resolve every thread it handled, so the PR shows only fixes that are published.

## Ending the loop

Ending the loop means finishing every actionable item of the current round, then opening no further rounds. Also end the loop when CI passes on the latest push and every actionable item is handled. Treat a review service that reports it is rate limited as having no new comments.

After ending the loop, stop the watcher and report:

- Each handled item in order: the original issue and what you actually did.
- Why the loop ended.
- Each review service that was rate limited, if any.
