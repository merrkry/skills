---
name: writing-agent-docs
description: How to write documents that agents read and act on, such as skills, AGENTS.md, READMEs, CONTRIBUTING and other project docs, and complex prompts like subagent briefs. Covers skill metadata, stating goals with bounded verification, requirement levels, phrasing, and revising. Use when creating or editing any of these.
---

# Writing Agent Docs

An agent reading the document should know what outcome is wanted, where to find what it needs, and how to tell when it is done. Assume the agent is a capable engineer who lacks only the project-specific context.

Decide what content earns a place with `/principle-document-only-what-code-cannot-say`. This skill covers how to write it.

## Skill metadata

A skill's `description` must have two parts: what the skill covers, and when to use it.

For a skill that should run only on the user's explicit request, disable implicit invocation in both places, and keep them in agreement:

```yaml
# SKILL.md frontmatter
disable-model-invocation: true
```

```yaml
# agents/openai.yaml
policy:
  allow_implicit_invocation: false
```

## State goals, not procedures

Describe the outcome each part of the document wants. Leave routine procedures, such as Git operations or running a test suite, to the agent.

When a task needs several mechanical steps, e.g. multiple complex commands or conditional control flow, put them in a script instead of pseudo-code in natural language, and have the document say when to run it.

Each part must give the agent:

- enough domain knowledge to decide the next action, or to know where to look it up;
- a clear check for its own work, with effort the document bounds. When the output can't be verified cheaply, state how far the agent should go, e.g. "one manual run of the affected command is enough", so verification doesn't grow the task's scope.

## Document roles

- A project-level `AGENTS.md` may include a brief map of the repository's top-level layout, even though a search would reveal it: the file is injected automatically, so the map helps the agent start faster.
- Treat a README as the introduction to its project or module. Put operational details, such as build, test, or deploy steps, in `AGENTS.md`, CONTRIBUTING, or scripts.
- In a prompt, such as a subagent brief, account for the `AGENTS.md` files and skills the recipient can access. Don't restate their guidance; name the skills the task needs and tell the recipient to load them.

## Requirement levels

Give every requirement and guideline explicit semantics, as in an RFC. Reserve plain present-tense statements for facts, such as how the code behaves. Write anything the agent or the project is expected to do with a requirement word or as an imperative: "tests must pass before merging", not "tests pass before merging".

Use the RFC 2119 levels, in lowercase unless the document already uses uppercase. Treat a bare imperative as `should`, and write hard requirements with `must`.

Keep facts, requirements, and assumptions apart. A default or an assumption is not a fact: write "by default, carry the PR until it is merged", not "the endpoint is the merged PR".

Use requirement levels generously: they are the way to state how firmly a rule holds, and the way to express emphasis, rather than capitals, bold, or repetition. Keep the background behind a rule to the minimum the agent needs to make a judgment call, and leave out background the environment already provides.

## Phrasing

State the behavior you want: the main case and the principle behind it, leaving exceptions to the agent's judgment. Qualify a rule with an adverb or a short adverbial phrase, such as "usually" or "without explicit authorization", rather than spelling out its exceptions or attaching a long `unless` clause, which complicates the sentence. Describe a criterion rather than enumerating the cases it covers. An agent handed a list tends to match against it mechanically; given the principle, it can judge cases the list would have missed.

Examples are optional, and an example reads as emphasis. Give one only when the concept is abstract, or when a specific case must be judged without ambiguity, not to illustrate common knowledge.

Add a counterexample where the required behavior differs from obvious common practice, e.g. "run tests with `scripts/test`, not `cargo test`: the script starts the database first". When revising a document after observing real runs, you may add a counterexample naming the behavior that went wrong.

## Revising

The document must be concise and unambiguous. Prose has no static checks, so incremental edits leave ambiguity and contradictions behind more easily than in code.

When revising, read the whole document, not just the part you're changing. Fix any passage that is ambiguous, redundant, contradicts another, or tells the agent what it would do anyway, including passages outside your change. Keep every fact and reason when rewording, especially recorded pitfalls. Leave passages that are already clear as they are.
