---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0006 — Derive the API reference from the installed toolchain

## Context

Language MCP servers that answer API questions derive them from the toolchain, not from frozen
files: gopls's package-API tool reads package source through the language server, and the Rust
documentation servers read rustdoc JSON, either from `cargo rustdoc` for the project's own
dependencies or from docs.rs. A frozen catalogue goes stale with every compiler release and knows
nothing about the packages a project has installed.

## Decision

- A module's reference is `pudu doc <file>` for that module's file, answered directly (about 70 ms).
- Declaration search uses an in-memory index built on first use from `pudu doc --json` and
  `pudu api --json` (public exports only) over every module file of the standard library and of
  each installed package under the workspace's `deps/`, run in bounded parallel chunks. The index
  lives for the process: a toolchain's library does not change while the server runs.
- Type-shape queries (containing `->`) go to `pudu search`, which unifies shapes.

## Consequences

- Reference answers match the compiler the user has, including installed packages.
- The first search costs one index build; later searches are in-memory.

## Rejected

- A generated catalogue ([[decisions/ADR-0002-compiled-documentation]], rejected alternative).
- Running `pudu search` over every file per query: about 7 s each.

## Referenced by

[[decisions/_MOC]] · [[tools/SyncDocs]] · [[decisions/ADR-0002-compiled-documentation]]
