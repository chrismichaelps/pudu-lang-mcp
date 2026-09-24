---
type: module
path: "@root/src/Constants/Server.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.2
depth_status: SHALLOW
coupling: 0
interface_stability: 0.8
tags: [module, shallow]
aliases: [Server Constants]
---

# Server constants

## Purpose

This server's identity, operating limits, caching hints, and the environment variable names it
reads.

## Interface

### Signatures

```pudu
export const SERVER_NAME: Str          // "pudu-lang-mcp"
export const SERVER_TITLE: Str
export const SERVER_VERSION: Str       // matches pudu.toml
export const INSTRUCTIONS: Str         // guidance returned by discovery and initialize
export const MAX_LINE_BYTES: Int       // longest accepted message line
export const COMMAND_TIMEOUT_MS: Int   // check, format, lint, docs, search
export const REFERENCE_TIMEOUT_MS: Int // one reference-indexing run
export const REFERENCE_OUTPUT_CAP_BYTES: Int  // bytes kept from one indexing run
export const REFERENCE_CHUNK: Int      // files per indexing run
export const REFERENCE_WORKERS: Int    // indexing runs at once
export const INDEX_WAIT_MS: Int        // longest wait for the background index
export const RUN_TIMEOUT_MS: Int       // default for pudu_run and pudu_test
export const MAX_RUN_TIMEOUT_MS: Int   // ceiling a caller may ask for
export const LSP_TIMEOUT_MS: Int
export const OUTPUT_CAP_BYTES: Int     // per stream of a child process
export const PAGE_SIZE: Int            // list page size
export const LIST_TTL_MS: Int          // tools, prompts, resources, templates, discover
export const DOCS_TTL_MS: Int          // documentation reads
export const STDLIB_TTL_MS: Int        // standard-library reads
export const DOC_CHARS: Int            // most characters of one document or reference
export const DEFAULT_SEARCH_LIMIT: Int
export const MAX_SEARCH_LIMIT: Int
export const ENV_PUDU_BIN: Str
export const ENV_PUDU_LIB: Str
export const ENV_WORKSPACE: Str
```

### Linkage

- **Requires:** std only.
- **Consumed by:** [[src/App/Discovery]], [[src/Services/Toolchain]], [[src/Main]], tool modules.

## Algorithm

Constants only.

## Negative Logic (Prohibited Paths)

- No limit is spelled as a literal elsewhere.

## Edge Cases

- `SERVER_VERSION` must equal the manifest version; the package test asserts it.

## Depth

DEPTH 0.2 (SHALLOW).

## Grill Log

- **Q:** How long may a line be? **A:** 8 MiB. _Rationale:_ a whole source file inlined as an
  argument must fit, while an unbounded line lets one message exhaust memory. _Rejected:_ no bound.
- **Q:** Deadlines? **A:** 20 s for compiler commands and language-server questions, 10 s default
  and 60 s ceiling for runs and tests. _Rationale:_ interpretation is slower than native code but
  an agent waiting longer than a minute is better told to run the command itself.

## Referenced by

[[src/Constants/_MOC]]
