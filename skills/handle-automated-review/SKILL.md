---
name: handle-automated-review
description: Judge and act on review findings from non-human sources, such as review bots on a PR or another agent's review. Use when handling such findings, whether posted on a PR or delivered locally.
---

# Handle Automated Review

An automated reviewer usually sees the change without most of the context behind it. Verify each finding rather than complying with it, and leave the code clearer with every outcome, including rejections.

## Verify before acting

Treat each finding as a claim to verify, and its proposed fix as a suggestion. Confirm or reproduce the problem before handling it, per `/principle-fix-root-causes` and `/test-audit`. When the finding comes with a reproduction, such as a failing test, run it instead of building your own.

Then take one of these paths:

- **Rejected**: the claim is factually wrong, or the case it guards isn't worth handling here, e.g. input the code already guarantees or treats as trusted, or an extreme edge case. Keep the behavior unchanged, and answer with the evidence.
- **Different fix**: the analysis holds, and your fix differs substantially from the suggestion. Apply it, and answer with what you did instead and why.
- **As suggested**: the analysis holds, and your fix matches the suggestion. Apply it without an answer.

Answer where the review lives, e.g. in the PR thread per `/babysit-pr`, or in the report of `/adversarial-review`.

## Fix at the scale of the problem

Treat the change under review as unfinished work: nothing requires keeping its current design or keeping the fix minimal. When a finding shows the design itself is wrong, go back to the original goal and redesign per `/principle-design-for-the-end-state`, even if that rewrites most of the change.

## Make the code answer the next reviewer

When a finding has no clear factual error, but you reject it or fix it differently for design reasons, the code likely fails to convey the design, and the next reader may misread it the same way. Besides answering, change the code so it conveys the design: make the intended structure visible in the code itself, state the background or tradeoff the code can't express, and delete leftovers that suggest a different design. Prefer code over prose, per `/principle-minimize-reader-load` and `/principle-document-only-what-code-cannot-say`. E.g. when a reviewer asks to validate input that is already validated at the boundary, carry that guarantee in a type per `/principle-boundary-discipline`, rather than only replying.

## Pitfalls

### Compensating for guarantees the design can't give

Findings often ask for a guarantee the current design can hardly provide, especially across process or system boundaries, e.g. cleanup that must run even if the process gets killed, or an atomic update spanning two services. Piling on retries, rollbacks, and compensation adds failure modes without delivering the guarantee. Instead, do one of:

- change the design so the guarantee holds by construction, e.g. give the state a single owner;
- lower the guarantee and state the weaker contract, e.g. at-least-once delivery;
- record it as a known issue, with when it occurs and its impact.

### Patching state bookkeeping

When findings keep landing on the same state, e.g. a flag falling out of sync or a missed transition, each patch adds another case to keep consistent. Stop patching: model the states and transitions as a type or state machine per `/principle-model-the-domain`, and restructure the control flow so each transition happens in one place.
