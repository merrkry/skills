---
name: principle-boundary-discipline
description: Place validation and error handling at system boundaries, and trust what the boundary guarantees inside. Use when parsing external input, handling errors, or adding retries, fallbacks, default values, rollbacks, or transactions.
---

# Boundary Discipline

Validate at system boundaries, and trust what the boundary already guarantees inside them.

## Boundaries

A boundary is where data enters from outside the program: CLI arguments, config files, environment variables, network, files, and external APIs.

- At the boundary, parse raw data into the program's own types, and convert foreign errors into the program's own.
- Express constraints in the language's type system, e.g. a non-null type, an enum, or a newtype for validated input. Inside the boundary, trust these types instead of checking them again. When the same check keeps showing up, change the types so they carry that guarantee.
- Handle each case once in the logic. Use a value the boundary already validated or defaulted as given deeper in the call chain.
- Expose domain concepts across the boundary, not the transport, storage, or framework types behind it.
- Keep business logic in plain functions with no framework or I/O dependencies, and keep the outer shell that does I/O thin.

## Errors

Each layer should handle the failures it can make a decision about, each in one place, and pass the rest up.

Retries, fallbacks, default values, forced overrides, and rollbacks add complexity and can hide bugs. Use one only where the layer can reliably fix that failure, e.g. retrying a network request or creating a missing directory. E.g. when a config loader fills in a default, consumers should use the value as given. When several layers repeat the same handling, consolidate it into the layer that owns it.

## Match the rigor to the code's lifetime

Let a one-off script, such as an ops task, fail loudly on bad input, without an error handling structure. Adding timeouts, retries, or recovery to it often makes it more complex and more fragile: a failure gets retried into a half-done state, or a timeout kills a step that was just slow. Let it stop, read the error, and rerun.
