---
name: test-audit
description: Standard for which tests to write, keep, or delete. Use whenever adding, changing, reviewing, or deleting tests, or changing code that is covered by tests.
---

# Test Audit

A test is worth keeping only if it catches a realistic bug in this project that no other test catches. Aim for confidence, not test count.

## Good tests

A good test:

- checks behavior that a user, caller, or other system can observe;
- fails for a realistic bug in this project's code;
- is the only test for that behavior;
- still passes after a refactor that keeps behavior;
- uses real entry points, not production code added just for tests.

Test from the outermost level where the behavior is still simple to observe and check exactly. The further a test is from implementation details, the fewer refactors break it. Go deeper only when the outer level makes the result hard to see or the setup heavy.

Don't test that a dependency works. Test how this project uses it. If unsure how a dependency behaves, read its source or types instead of writing a test.

Keep a test that is the only guard of a contract other code depends on, such as a public API, file format, config, migration, storage, or security rule, even if it looks like it checks implementation. Slow or static is not a reason to delete it.

Prefer adding a case to an existing test over writing a near-copy.

### Behavior changes

Not every behavior change is a bug fix:

- **Bug fix**: the code does not do what it was clearly meant to do, judged by its name, docs, types, or contract. Add one regression test that fails before the fix and passes after.
- **Intended change**: the code does what it was written to do, but someone wants something else. This includes "users expected X" reports. Treat it as a feature change: update existing tests to describe the new behavior, and add a test only if it passes the bar above. Don't keep or add tests for the old behavior.
- **Removal**: delete, merge, or simplify the tests that covered the removed code. Don't add tests that check it is gone or no longer called.

## Bad tests

- No assertions, only there for coverage.
- Compares a value with itself or with a copy of its input.
- Searches source code for a string, import, or name.
- Tests private helpers already covered through the public interface.
- Repeats a check another test already makes.
- Keeps test-only exports, wrappers, or otherwise dead production code alive.
- Computes the expected value with the code under test.
- Re-implements the code under test, e.g. mirrors its branches, loops, or calculations step by step. Such a test only proves the code does what it already does.
- Mocks or fixtures do the work the test claims to check, so it passes even if the code is broken.
- Checks that a flag or setting exists, not what it does.
- Negative test that passes for an unrelated reason.
- Name claims more than the test checks.

## Boundaries

- Read the whole test, the code it covers, related tests, and git history before judging it.
- Only delete a test when you can say what else covers it, or why nothing needs to. When unsure, keep it and report it.
- When a test is removed, also remove the test-only code it kept alive.
- If a test you keep fails, suspect a real bug and fix the code, not the test.
- In an audit, report candidates before editing unless the user asked for changes.
- Only commit, push, or open a pull request when the user allows it.

## Deliverables

Run the formatting, static checks, and tests the project requires, and make them pass.

Tell the user:

- which tests were added, changed, or removed, and why;
- production code simplified or deleted;
- questionable tests that were kept, and why;
- checks run and their results.
