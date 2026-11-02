---
name: agent-delegation
description: Model, effort, and tool choice for subagents and threads, through native tools or T3 Code. Use before dispatching a subagent, delegating a task or review, or launching a thread.
---

# Agent Delegation

Choose the model and effort first, then the tool that can run them.

## Model and effort

Settle the configuration by how much of it is given, from any source:

1. **Full**: the provider, model, and effort are all given. Use them as given.
2. **Partial**, e.g. only a model: keep what is given, and infer the rest as in the next case. Run a GPT or Claude model on `codex` or `claudeAgent` respectively. On a provider other than this thread's, take the model at the same rank as this thread's.
3. **None**: use this thread's model and effort. A simpler task may take a lower model from the same family, or a lower effort:
   - Claude: Haiku < Sonnet < Opus < Fable.
   - GPT: Luna < Terra < Sol < Astra.

An inferred effort must not be `max`, `ultra`, `ultracode`, or `ultrathink` unless this thread already runs at `max`.

## Tool

Prefer the current provider's native subagents, e.g. Claude Code's `Agent` or Codex's `spawn_agent`, when they can run the chosen model and effort.

Otherwise, use T3 Code, with provider and model IDs from `orchestrator_capabilities`. To run with this thread's provider, model, and options, leave `target` (`delegate_task`, `create_threads`) or `modelSelection` (`t3_thread_launch`) unset.
