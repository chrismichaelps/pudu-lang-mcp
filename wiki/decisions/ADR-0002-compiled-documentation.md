---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0002 — Compile the documentation into the server

## Context

A bundle built with `pudu build` is one file, and a program cannot learn its own location, so
documentation shipped beside the sources cannot be found at run time. Compile-time code may not
read files.

## Decision

`tools/SyncDocs.pudu` reads the published prose from a pudu-lang checkout — the language guide,
site pages, release notes, and playground examples — and writes `src/Generated/Docs.pudu`: one `const` array of chapters with their slug,
title, and Markdown, escaped for Pudu string literals. The server splits chapters into sections at
start-up. The generated file records the documentation revision it came from.

## Consequences

- The server answers documentation questions with no network and no files.
- A documentation change reaches the server when the generator is re-run and committed.
- Only prose is compiled in. This is what language servers with an MCP surface do for their
  guidance (gopls embeds its model instructions); reference material is derived from the toolchain
  instead ([[decisions/ADR-0006-reference-from-toolchain]]).
- The prose is about 190 KB and one array literal of 51 documents; the compiler's parser nesting
  limit (reached near 500 elements in one literal) is far away.

## Rejected

- Generating the standard-library catalogue too: 3,500 entries exceed the parser's nesting limit in
  one literal, freeze the reference at one compiler version, and miss installed packages.

- Fetching from the website at run time: needs a network and breaks offline use.
- Reading a directory named by an environment variable only: fails for anyone who installs the
  package without a pudu-lang checkout.

## Referenced by

[[decisions/_MOC]] · [[src/Generated/Docs]]
