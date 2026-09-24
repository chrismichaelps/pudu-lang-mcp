---
type: changelog
tags: [changelog]
---

# Changelog

## 2026-09-23 — Publication baseline

- `pudu.toml` is the canonical library manifest: `@chrismichaelps/pudu-lang-mcp` 0.1.0 with
  description, license, keywords, the `>=0.1.1 <0.2.0` language range, `src` as its source, and
  `PuduLangMcp` as its module root.
- The end-to-end suite reads replies through `List.get`, so a server that writes fewer lines than
  expected, such as one started by an older compiler, reports each failed check by name instead of
  stopping the suite at an index.
- A bounded run no longer waits on its output readers without limit. Once the child has exited or
  been stopped, the readers have 250 ms to reach the end of their streams; a stream a grandchild
  keeps open answers what was read and is marked cut ([[src/Services/Process/Bounded]]).

## 2026-09-23 — Package validation

- Completed publishable package metadata: description, Apache-2.0 license, and discovery keywords.
- Added exact deadline checks for reference-index waits and child processes, explicit wait-error
  propagation, malformed-resource-path coverage, and source-excerpt boundaries.
- Removed unused protocol and JSON helper declarations and redundant branches uncovered by
  mutation testing. The pull-request mutation sample now requires every valid mutant to be killed.
- The publication prerequisite is recorded in [[handoffs/2026-09-23-package-publication]].

## 2026-09-23 — Initial server

- Dual-era MCP server over stdio: modern `2026-07-28` per-request metadata and the legacy
  `initialize` handshake ([[decisions/ADR-0001-dual-era-protocol]]).
- Nineteen tools across documentation, API reference, compiler, language server, and toolchain;
  documentation, example, and module-reference resources; four prompts; argument completion.
- The published prose is compiled in ([[decisions/ADR-0002-compiled-documentation]]); the API
  reference is derived from the installed toolchain ([[decisions/ADR-0006-reference-from-toolchain]]).
- Unit, application, services, end-to-end, and mutation testing ([[architecture/TESTING]]).

## Referenced by

[[00-INDEX]]
