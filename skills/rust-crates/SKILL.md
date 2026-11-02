---
name: rust-crates
description: Preferred crates for common needs in Rust projects. Use when choosing or adding a Rust dependency.
---

# Rust Crates

Prefer the crates below, and crates from their ecosystems. Use them instead of reimplementing what they provide, unless the project must stay dependency-free.

## Basic Building Blocks

- `anyhow`: Flexible concrete Error type built on `std::error::Error`
- `bon`: Next-gen compile-time-checked builder generator, named function's arguments, and more!
- `itertools`: Extra iterator adaptors, iterator methods, free functions, and macros
- `rayon`: Simple work-stealing parallelism for Rust
- `thiserror`: `derive(Error)`

## Recommended

Default to these, and pick another crate when the use case calls for it.

- `clap`: A simple to use, efficient, and full-featured Command Line Argument Parser
- `jiff`: A date-time library that encourages you to jump into the pit of success. This library is heavily inspired by the Temporal project
- `reqwest`: higher level HTTP client library
- `rustix`: Safe Rust bindings to POSIX/Unix/Linux/Winsock-like syscalls
- `serde`: A generic serialization/deserialization framework
- `sqlx`: 🧰 The Rust SQL Toolkit. An async, pure Rust SQL crate featuring compile-time checked queries without a DSL. Supports PostgreSQL, MySQL, and SQLite.
- `tokio`: An event-driven, non-blocking I/O platform for writing asynchronous I/O backed applications
- `tracing`: Application-level tracing for Rust.

## Maintaining this skill

When the user asks to add a crate to this skill, add a bullet to the matching list, keeping each list in lexicographical order, with the exact crate description from `crates.io`.
