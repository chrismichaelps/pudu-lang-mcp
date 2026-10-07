---
type: changelog
tags: [changelog]
---

# Changelog

## 2026-10-07 — Pudu 0.1.3 and derives (#11)

- The bundled corpus is regenerated from pudu-lang `v0.1.3`: the Derives and Deploying chapters,
  the 0.1.2 and 0.1.3 release notes, and the derives example join it — 56 documents
  ([[src/PuduLangMcp/Generated/Docs]], [[tools/SyncDocs]]).
- The reference keeps the methods of exported traits and types and the implementations of an
  exported trait's methods, so `Std.Meta`'s `get`, `set`, `has`, and `attributeOr` are listed; each
  type and trait shows its declaration with its `derives` clause; and every `export derive`
  strategy is listed with its documentation, read from source because the compiler's JSON omits
  it ([[src/PuduLangMcp/Domain/Reference/Decode]], [[src/PuduLangMcp/Domain/Reference/Derives]],
  [[src/PuduLangMcp/Domain/Reference/Entry]]).
- The compiler's reference JSON is decoded through records that `derives Json.Decode`, and the
  prompt catalogue encodes through `derives Json.Encode`
  ([[src/PuduLangMcp/Domain/Catalog/Prompts]]).
- Every prompt carries the derive rules: the clause, the derivable traits, field attributes,
  strategies over `Std.Meta`, and the `Meta.collect` trap.
- The server reported version 0.1.0 while the package was 0.1.1; it now reports the manifest's
  version, 0.1.2, and `test/Package/LayoutTest` keeps the two equal
  ([[src/PuduLangMcp/Constants/Server]]).
- The instructions every client receives named tools that never existed (`pudu_stdlib_search`,
  `pudu_stdlib_module`); they now name `pudu_reference_search` and `pudu_module_reference`, and a
  suite checks every tool they mention.
- A built server indexed only the 19 modules bundled into itself: the executable sets `PUDU_LIB`
  to its own bundle ([chrismichaelps/pudu-lang#460](https://github.com/chrismichaelps/pudu-lang/issues/460)).
  A bundle directory is now passed over, `PUDU_MCP_LIB` names the library explicitly, and the
  reference covers the installed library — 214 modules on 0.1.3
  ([[src/PuduLangMcp/Services/Toolchain]]).
- `pudu_expand` answers the implementations a file's derives generate
  ([[src/PuduLangMcp/App/Tools/Compiler]]).
- CI and the language range move to Pudu 0.1.3 (`>=0.1.3 <0.2.0`)
  ([[handoffs/2026-10-07-pudu-0-1-3]]).

## 2026-09-24 — Documentation refreshed from pudu-lang dev (#9)

- The bundled corpus is regenerated at pudu-lang `dev` 645c7113, which corrects the Collections
  chapter: `items.get(i)` stops the program at a missing position, and `List.get(&items, i)` answers an
  `Option` ([[src/PuduLangMcp/Generated/Docs]]). A corpus check pins the corrected sentence.

## 2026-09-24 — Module root ownership (0.1.1)

- Every module the package ships moved under its declared root: `src/PuduLangMcp/**` holds
  `PuduLangMcp.App`, `.Domain`, `.Services`, `.Utils`, `.Constants`, `.Errors`, and `.Generated`,
  with suites mirrored under `test/PuduLangMcp/`. In 0.1.0 those modules sat at the top level, and
  a program with its own `App.Context` stopped compiling once it installed the package.
- `test/Package/LayoutTest` refuses a shipped module outside the root or misnamed for its path
  ([[architecture/TESTING]]); the rule is an architectural law in [[grammar/pudu]].
- The package version is 0.1.1; the mirror is indexed from [[src/PuduLangMcp/_MOC]].
- Released 0.1.0 and 0.1.1; www.pudu-lang.org lists the package with 0.1.1 as latest
  ([[handoffs/2026-09-23-package-publication]]).

## 2026-09-23 — Publication baseline

- `pudu.toml` is the canonical library manifest: `@chrismichaelps/pudu-lang-mcp` 0.1.0 with
  description, license, keywords, the `>=0.1.1 <0.2.0` language range, `src` as its source, and
  `PuduLangMcp` as its module root.
- The end-to-end suite reads replies through `List.get`, so a server that writes fewer lines than
  expected, such as one started by an older compiler, reports each failed check by name instead of
  stopping the suite at an index.
- A bounded run no longer waits on its output readers without limit. Once the child has exited or
  been stopped, the readers have 250 ms to reach the end of their streams; a stream a grandchild
  keeps open answers what was read and is marked cut ([[src/PuduLangMcp/Services/Process/Bounded]]).

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
