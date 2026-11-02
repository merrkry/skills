---
name: local-repos
description: Where local Git repositories live and how to set up clones and forks. Use when the user asks to clone or fork a repository, or when locating a local checkout, reading one as reference, or configuring Git remotes.
---

# Local Repositories

Local repositories are at `~/Projects/<owner>/<repo>`, e.g. `~/Projects/NixOS/nixpkgs`, and our own repos are under `merrkry`. Clone new repositories into the same layout.

Clone huge codebases with `--filter=blob:none`, and configure every remote of such a clone with `remote.<name>.promisor=true` and `remote.<name>.partialclonefilter=blob:none`.

For forks:

- Put the fork under the upstream owner, e.g. `~/Projects/NixOS/nixpkgs`.
- Put it under `merrkry` instead for a hard fork or a renamed repo.
- Name the fork remote `origin` and the upstream remote `upstream`.

## Reading a checkout as reference

A local checkout may be stale: its branches can lag their remotes, and a fork can lag upstream.
Fetch freely, then read the up-to-date remote branch, e.g. `git grep <pattern> upstream/master` or `git show upstream/master:<path>`, leaving the working tree as it is.
