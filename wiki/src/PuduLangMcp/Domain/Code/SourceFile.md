---
type: module
path: "@root/src/PuduLangMcp/Domain/Code/SourceFile.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MODERATE
coupling: 0
interface_stability: 0.9
tags: [module]
aliases: [Source File]
---

# SourceFile

## Purpose

Where inline source must be written so the compiler accepts it: the file path its `module`
declaration implies, and the module name a file path implies.

## Interface

### Signatures

```pudu
export fn moduleNameOf(source: Str) -> Option[Str]
export fn fileOf(moduleName: Str) -> Str            // "App.Greeting" → "App/Greeting.pudu"
export fn moduleOfFile(relative: Str) -> Option[Str] // "Std/Json.pudu" → "Std.Json"
export fn isModuleName(name: Str) -> Bool
```

### Linkage

- **Requires:** `Std.Text`.
- **Consumed by:** [[src/PuduLangMcp/Services/Workspace]], [[src/PuduLangMcp/Services/Reference]].

## Algorithm

- `moduleNameOf`: the first line that is not blank and not a comment (`//`, `///`, `/**` … `*/`
  on one line) must be `module <Name>`; the name is answered when `isModuleName` holds.
- `isModuleName`: dot-separated segments, each starting with an ASCII letter (`Char.isLetter`) and
  continuing with ASCII letters, digits (`Char.isAlphanumeric`), or `_`.
- `moduleOfFile`: strip `.pudu`, replace `/` with `.`, and require `isModuleName`.

## Negative Logic (Prohibited Paths)

- A module name never produces a path containing `..` or starting with `/`.

## Edge Cases

- Source without a module declaration answers `None`; the caller writes it as `Main.pudu`.

## Depth

DEPTH 0.4.

## Grill Log

- **Q:** Why must the file path follow the module name? **A:** The compiler requires a root file's
  module name to match its path under the source root; writing `module App.Greeting` to
  `Main.pudu` would fail for a reason unrelated to the code.

## Referenced by

[[src/PuduLangMcp/Domain/Code/_MOC]]
