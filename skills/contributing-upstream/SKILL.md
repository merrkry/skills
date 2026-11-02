---
name: contributing-upstream
description: Conventions for posting to projects we don't own. Use when opening a discussion, issue, or pull request on an upstream repository, or emailing its maintainers or mailing list.
---

# Contributing upstream

Write every report or contribution so an upstream maintainer can act on it without knowing anything about our setup.

Before writing, search existing issues, discussions, and PRs, and link the related ones.

## Follow upstream's rules

Read upstream's contributor docs: general contributing guidelines, issue and PR templates, and LLM usage policy. They decide:

- the venue, e.g. questions and proposals in discussions, bugs in issues, changes as PRs;
- the required format, e.g. template sections, commit message style, sign-off, changelog entries;
- the LLM usage disclosure. When the docs ask for one, add it in the form and place they require.

For security-related reports, follow upstream's security disclosure process first; it overrides the venue and format above.

## Write it self-contained

Our use case is the trigger, never the content. Leave out every local detail, such as our hosts, paths, repo layout, and the story of how we hit the problem. When a reproduction needs configuration, write a fresh minimal one from scratch instead of trimming ours.

Assume the maintainer has full context on their project. Give only the minimal evidence needed to verify the report or carry out the fix, and leave out the rest. When reporting a defect, include at least one of:

- **Code logic analysis.** Walk through upstream's code path and point to where the behavior goes wrong, citing permalinks pinned to a commit SHA.
- **Minimal reproduction.** Give the smallest steps or snippet that show the behavior on a clean setup. Run it yourself and confirm it reproduces before posting.

State expected and actual behavior in terms of upstream's own interfaces.

When the problem involves another service or project, link its documentation or code for each claim about its behavior.

## Posting

Before posting a long text, such as a PR body, an issue, or an email, show the user the full text and get their confirmation.

Without explicit authorization, you must not send anything through a channel that doesn't support editing or retracting, such as email or a mailing list.

Open PRs as drafts by default.