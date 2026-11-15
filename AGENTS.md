# AGENTS.md

Personal agent skills.

## Layout

- `skills/<name>/SKILL.md`: one skill per directory, with any references or scripts it needs next to it.
- `skills/<name>/agents/openai.yaml`: optional Codex metadata, such as the implicit-invocation policy.

Consumers install skills with [yaasm](https://github.com/merrkry/yaasm), which reads this repository as a GitHub source and selects each skill by its path, `skills/<name>`. Renaming or moving a skill directory therefore breaks consumers that select it; treat the path as a public interface.

## Writing skills

Load the `writing-agent-docs` skill, from `skills/writing-agent-docs`, before creating or editing a skill. It covers skill metadata, phrasing, and requirement levels.
