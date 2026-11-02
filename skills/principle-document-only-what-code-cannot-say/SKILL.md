---
name: principle-document-only-what-code-cannot-say
description: Whether to add code comments, doc comments, or prose docs, what they should say, and what to cut. Use when writing, reviewing, or cleaning up comments or documentation.
---

# Document Only What Code Cannot Say

Write for someone who knows only the current code, not its history or author. Each comment should make one point the reader needs here and can't get from the code.

Documentation is optional. Before adding a comment or doc of any kind, decide whether the reader needs it. A code change that adds no docs is the normal case.

Comments written during a change tend to describe the change. Before finishing, check each comment you wrote or edited against the review steps below.

## Who reads what

- Doc comments serve callers. State the contract: what it does, what the types don't say, when it fails, and what the caller must ensure. It should stay true if the body is rewritten with the same behavior.
- Inline comments serve maintainers. Answer their question at this line: why it is this way, or what breaks if it changes. Don't narrate the next lines.
- Prose docs serve someone arriving cold. Cover concepts, usage, and decisions spanning files. Don't mirror the code or repeat contracts owned by doc comments. Leave out details that break easily when the implementation changes. For how to write them, also load `/writing-agent-docs`.

Match the scope of the existing docs, in the current file and in similar files nearby. Keep implementation details out of user-facing docs, and low-level details out of high-level docs.

Tell each fact once per audience. Give a repeated fact one home and delete the other copies. Keep a pointer elsewhere only when it states an obligation, e.g. "new routes also need approval in infra".

## Point to the code instead of restating it

Leave out what a few local searches or one command reveal: file lists, function names, config values, script contents, command flags. A copy goes stale when the source changes. Name the entry point instead: the file, command, or doc where the reader should start. Write down what the reader can't find by looking, such as unwritten conventions and pitfalls the code doesn't reveal.

A sentence like "A handles X, B handles Y, and C handles Z1 and Z2" restates implementation details. It goes stale as soon as someone adds a D or a Z3. For such a sentence:

1. Ask whether it needs to exist. Usually the code already says it.
2. If it does, replace it with a high-level description of what the parts have in common, e.g. "each backend handles one storage format".
3. If specific items matter, list only representative or important ones, and say the list is not complete, e.g. "such as" or "e.g.".

## Prefer code over comments

A fact often belongs in a name, type, error message, assertion, or test. Prefer those homes.

If our own code behaves surprisingly, change the code so the behavior is obvious: rename, extract, or add a type. Don't explain the surprise in prose. Comments about surprises are for things we can't change, such as a dependency, platform, or protocol.

## Describe the present

Put history in the work trail, such as commit messages, not in code or docs.

- Delete history that depends on knowing an earlier version: "now", "no longer", "instead of", "replaced", "refactored", "originally". Judge the meaning, not the word.
- Delete plans: "will", "eventually", "not implemented yet". Turn intended work into a TODO or an issue link.
- When history explains a rule that still holds, keep the rule and drop the story. E.g. "we used to cache this token, but that broke login" becomes "don't cache this token: it changes on every request".
- When removing an abstraction, remove the docs that described it.

## Keep

- Safety justifications and invariant contracts.
- Error and panic contracts callers rely on.
- Why the obvious approach wasn't taken, stated as what goes wrong with it.
- Notes marking a deliberate decision.
- Links to issues, specs, or upstream bugs behind a workaround.
- A link to the source of a snippet copied from elsewhere, such as upstream docs, when the snippet should stay in line with it.
- License and attribution notices, doctests, and the project's doc conventions.

## Cut

- Restated signatures and types, and narration of the body.
- Change logs: "Refactored to...", "Fixed the bug where...", "Added support for...".
- Commented-out code and completed TODOs.
- Empty openers and sequence markers: "Note:", "This function...", "Helper to...", "// Step 2".
- Tours of where else a topic lives, when nothing at this line depends on it.
- Comments in tests announcing what the next assert checks. Name the case instead.
- Hype and padding: "robust", "seamless", "simply", "just".
- Doc files that serve as work logs, such as summaries, plans, and status reports.

## Scope

Apply these rules to comments you write or edit, and to comments the change makes wrong or outdated. Leave other existing comments alone, even when refactoring nearby code: they may record background such as past pitfalls, and rewording them widens the diff and muddies blame. Don't reword a comment just to restyle it.

When a prose doc itself is what you're revising, follow the revising rules in `/writing-agent-docs` instead.

## Reviewing comments

For each comment, check in order and act on the first failure:

1. True? If it disagrees with the code and you can't tell which is wrong, flag it. Don't rewrite it to match a possible bug.
2. Needed? Delete it if the name, types, nearby strings, or next few lines already say it.
3. About the current code? Delete history and plans as above.
4. At the fact's home? If not, delete it or turn it into an obligation pointer.
5. Clear at a glance? One point, no filler or hedging.

Keep a comment that passes word for word. When comments mirror the code's structure one to one, cut the whole class, not single instances.

Shorten by cutting points, not by compressing words. Keep connectives like "because" and "otherwise" that explain the reason.
