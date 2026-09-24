---
type: grammar
language: Pudu
version: "0.1.1"
tags: [grammar]
aliases: [Grammar — Pudu, Pudu Grammar]
---

# Grammar — Pudu

The Pudu surface this repository is written against, pinned to compiler `0.1.1`. Where this
page and the compiler disagree, the compiler wins and this page is corrected in the same change.

## SDK Discovery Map

| Need | Module | Entry points |
| --- | --- | --- |
| Line-framed stdio | `Std.Io` | `readLineOrEnd`, `writeLine`, `writeErrorLine`, `read`, `write`, `list`, `exists` |
| JSON values | `Std.Json` | `decode`, `encode`, `field`, `path`, `object`, `list`, `asText`, `asInt`, `asObject`, `asList`, `asBool`, `explain` |
| Processes | `Std.Process` | `start`, `writeText`, `closeInput`, `read`, `readErrors`, `waitWithin`, `stop`, `run` |
| Environment | `Std.Env` | `variable`, `variableOr`, `searchPath`, `temporaryDirectory`, `elapsedMilliseconds`, `all` |
| Paths | `Std.Path` | `join`, `normalize`, `isAbsolute`, `resolve`, `isInside`, `nameOf`, `directoryOf`, `relativeTo` |
| Confinement | `Std.Fs` | `canonical`, `resolveInside`, `temporaryDirectoryIn`, `removeTree` |
| Threads | `Std.Concurrent`, `Std.Sync` | `Concurrent.start`, `join`; `Sync.cell`, `get`, `set` |
| Collections | `Std.List`, `Std.Map`, `Std.Set` | `List.get`, `List.first`, `List.find`, `List.sortBy`, `List.take`; `Map.get`, `Map.getOr` |
| Tests | `Std.Test` | `suite`, `equals`, `that`, `present`, `absent`, `run`, `report` |

## Imports / Namespaces

- One module per file; the module name is the path under its source root with `/` as `.`:
  `src/Domain/Rpc/Message.pudu` is `module Domain.Rpc.Message`.
- Every import is qualified and aliased: `import Std.Json as Json`. Nothing is imported implicitly.
- Suites under `test/` import project modules from `src/` through the manifest's source root.

## Core Primitives

- Records: `export type Point = { x: Int, y: Int }`, built as `Point{x: 1, y: 2}`, updated as
  `Point{..p, y: 5}`.
- Sum types: `export type Id = NumberId(Int) | TextId(Str)`, taken apart with `match … { case … => … }`.
- `Option[T]` and `Result[T, E]` are ordinary sum types. Their helpers are module functions
  (`Option.unwrapOr(value, fallback)`), never methods.
- `?` propagates `None` or `Err` from a function whose return type is the same family.
- `if let Some(x) = value { … }` and `let … else` destructure one case.
- Module scope holds only `const`. A `const` initialiser runs at compile time.
- Lookup tables are `const` tables built with the built-ins `mapOf([...])` and `setOf([...])`,
  never `if` ladders or `match` chains over strings.
- `comptime fn` computes constants; it may not perform effects.
- Closures: `fn(x: Int) -> Int { x + 1 }`, or the short form `|x: Int| x + 1`.
- Borrowing: `&T` parameters are read-only views; a function that must own a value takes `T`.
  `*view` copies a borrowed value into an owned one.

## Architectural Laws

- Dependency direction is inward: `Main → App → Services → Domain → Utils/Constants/Errors`.
  `Domain` never imports `Services` or `App`; nothing imports `Main`.
- Every effect (process, file, clock, environment, stdio) lives in `Services/` or `Main`.
  `Domain` modules are pure functions over values.
- Compiler access goes through the [[seams/Toolchain]] record, so the protocol core is testable
  without starting processes.
- Failures are values: `Result` with a typed error from `Errors/`. No failure is swallowed
  without a written reason.

## Syntax Rules / Naming

- Types, traits, modules, and variants are `PascalCase`; values `camelCase`; constants
  `UPPER_SNAKE_CASE`.
- Every file header and exported type carries the FMCF anchor, one line:
  `/** @Namespace.Entity.Role — intent */`, five to eight words of intent.
- Every `fn`, `export fn`, and `const` carries a `///` doc comment of one or two lines stating
  what it answers or holds, in the voice of the standard library's own documentation
  ("The value, or a fallback when the operation failed."). It states the contract, not the steps.
- Rationale belongs in the mirrored page's Grill Log. No narration, history, or explanation of the
  obvious in code.

## Prohibited Patterns (verified against the 0.1.1 compiler)

- **A brace inside a string literal is interpolation.** `"{name}"` interpolates. A literal brace
  is `\{` or `\}`; JSON written in a literal needs both escaped.
- **`Array.get(i)` answers the element and stops the program when `i` is out of range.** Use
  `List.get(&items, i)` or `List.first(&items)` for an `Option`. (`Map.get` does answer `Option`.)
- **`Map.fromPairs` is not usable in a `const` initialiser**; use `mapOf([...])`. A `const`
  initialiser may name this module's functions but not an imported module's; a table of imported
  handlers is a function returning its `mapOf` literal.
- **`scope`, `module`, `where`, and `task` are keywords**; none can name a binding or a record field
  (`moduleName`, not `module`).
- **`&-1` lexes as the operator `&-`**; borrow a negative literal through a named constant or `&(-1)`.
- **`Str.indexOf` answers `-1` when absent**; prefer `Text.find` when absence must be matched.
- No `print`-style debugging on stdout: stdout carries only protocol messages
  ([[domain/Transport]]). Diagnostics go to stderr.

## Senior Definition Needed

(none open)

## Referenced by

[[00-INDEX]] · [[architecture/_MOC]]
