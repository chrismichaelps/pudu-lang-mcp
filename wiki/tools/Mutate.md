---
type: module
path: "@root/tools/Mutate.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: DEEP
coupling: 2
interface_stability: 0.8
tags: [module, tool, test]
aliases: [Mutate, Mutation Testing]
---

# Mutate

## Purpose

Measure whether the test suite notices wrong code: make one small change to the source at a time,
run the suites, and report every change they did not catch.

## Interface

```bash
pudu run tools/Mutate.pudu [--every N] [--threshold P] [--dry-run] [--file src/...]
```

- `--every N` keeps every Nth mutant (deterministic sampling), default 1.
- `--threshold P` exits 1 when the score is below P percent, default 0.
- `--dry-run` lists the mutants without running anything.
- `--file` limits mutation to one file; default every file under `src/` except `src/PuduLangMcp/Generated/`
  and `src/Main.pudu`.

The compiler is `PUDU_BIN` or `pudu` on the search path.

## Algorithm

1. Mutants. For each line that is not a comment line, find the code characters: everything before a
   `//` that is outside a string literal, excluding string literal contents (escapes respected).
   Each occurrence of an operator in code characters is one mutant:

   | From | To | From | To |
   | --- | --- | --- | --- |
   | `==` | `!=` | `!=` | `==` |
   | `<=` | `<` | `>=` | `>` |
   | `<` (not `<=`, `<-`, `->`) | `<=` | `>` (not `>=`, `->`, `=>`) | `>=` |
   | `&&` | `\|\|` | `\|\|` | `&&` |
   | `true` | `false` | `false` | `true` |
   | `+ 1` | `- 1` | `- 1` | `+ 1` |
   | `!` before a name or `(` | removed | | |

   `true` and `false` count only as whole words. Mutants are ordered by file, line, column, operator.
2. Sampling keeps mutants whose index is a multiple of `every`.
3. For each mutant: write the mutated file; `pudu check` it (a mutant that does not compile is
   *invalid* and not scored); otherwise run `pudu test test` with `MUTANT_TIMEOUT_MS` — a failing
   or timed-out run *kills* the mutant, a passing one lets it *survive*. The original text is
   written back after every mutant, before anything else happens.
4. Report each survivor as `file:line:column  from → to`, then
   `killed K, survived S, invalid I, score P%` with `P = K / (K + S)`.

## Negative Logic (Prohibited Paths)

- The original file is never left mutated: it is restored even when a run fails to start.
- Generated code and the one-line entry point are never mutated.

## Edge Cases

- No mutants: score 100%.

## Grill Log

- **Q:** Why text operators instead of syntax-tree mutation? **A:** The operators chosen are
  unambiguous in Pudu's surface once strings and comments are excluded, and the compile check
  discards any mutant that is not valid Pudu; a tree mutator would duplicate the parser.
- **Q:** Why run the whole suite per mutant? **A:** Suites are per module but a mutant in a
  shared module can be caught anywhere; the whole suite takes a few seconds.

## Referenced by

[[00-INDEX]] · [[tools/SyncDocs]]
