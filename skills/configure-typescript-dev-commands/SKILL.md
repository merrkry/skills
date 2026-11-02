---
name: configure-typescript-dev-commands
description: Configure or standardize build, check, dev, format, lint, test, and typecheck commands in a TypeScript repository. Use when setting up package.json scripts, updating the related development tools, or providing static analysis tools for agents.
disable-model-invocation: true
---

# Configure TypeScript Dev Commands

By default, use `pnpm`, `oxfmt`, `oxlint`, `vitest`, and strict TypeScript type checks. Treat the snippets below as examples, and adapt them to the repository's layout, tech stack, and conventions, and to the user's requirements.

Install or update the selected tools through the package manager, so it resolves the latest compatible versions, rather than writing versions from memory.

Invoke scripts with `pnpm run <name>`, e.g. `pnpm run check`, not `pnpm check`: the shortcut can collide with a built-in pnpm command.

Example `package.json` scripts:

```json
{
  "scripts": {
    "build": "tsc",
    "check": "pnpm run format:check && pnpm run lint && pnpm run typecheck && pnpm run build && pnpm run test",
    "dev": "tsc --watch",
    "format": "oxfmt .",
    "format:check": "oxfmt --check .",
    "lint": "oxlint .",
    "test": "vitest run",
    "typecheck": "tsc --noEmit"
  }
}
```

Add `dev` only when the project needs a watch loop, e.g. a website. Keep valid existing scripts unrelated to these tools.

When orchestration outgrows a one-line script, move it into a script such as `scripts/check.mjs` and invoke that from `package.json`.

Document the commands in the repository's `AGENTS.md`, in an existing relevant section or a new one, for example:

```markdown
## Verification

- Use static analysis for feedback. Start a dev server or preview only when explicitly asked.
- Use `pnpm run format` for formatting.
- Use `pnpm run check` for full static analysis. It must pass before the work counts as done.
```

Finish by running `pnpm run format`, then make `pnpm run check` pass.
