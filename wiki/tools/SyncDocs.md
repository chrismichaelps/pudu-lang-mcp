---
type: module
path: "@root/tools/SyncDocs.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.45
depth_status: MODERATE
coupling: 1
interface_stability: 0.8
tags: [module, tool]
aliases: [SyncDocs]
---

# SyncDocs

## Purpose

Regenerate [[src/PuduLangMcp/Generated/Docs]] from a pudu-lang checkout: the language guide, the site pages,
the release notes, and the playground examples ([[decisions/ADR-0002-compiled-documentation]]).

## Interface

```bash
pudu run tools/SyncDocs.pudu <pudu-lang-checkout> [revision]
```

A released corpus comes from a tag: `git archive v0.1.3 website packages/pudu/v0.1/release-notes
CODE_OF_CONDUCT.md CONTRIBUTING.md SECURITY.md | tar -x -C <dir>`, then run the tool on `<dir>`
with the revision `v0.1.3`.

Writes `src/PuduLangMcp/Generated/Docs.pudu`. Exit status 0 on success, 1 with a message on stderr
otherwise.

## Algorithm

1. Collect, in this order: `website/docs/*.md` (group `docs`); the six site pages the website
   publishes (group `pages`: conduct, contributing, security, community, privacy, brand); every
   `packages/pudu/<series>/release-notes/*.md` (group `releases`, slug = version); and
   `website/playground/examples/*.pudu` (group `examples`, wrapped in a `pudu` fence and titled by
   the program's first `// ` line). Each directory is sorted by name.
2. A slug is the file name without its extension and without a leading `NN-` prefix; a title is
   the first line starting `# `, else the slug.
3. Escape each chapter's text for a Pudu string literal: `\` `"` `{` `}` are backslash-escaped,
   a line break is `\n`, a tab `\t`, and carriage returns are dropped.
4. Write the module: the revision constant, then one `Chapter{...}` per document in `CHAPTERS`.

## Negative Logic (Prohibited Paths)

- Never writes a partial file: the text is built fully, then written once.

## Edge Cases

- No documentation chapters is an error: an empty corpus would silently disable the docs tools.
- The standard-library catalogue is deliberately not generated
  ([[decisions/ADR-0006-reference-from-toolchain]]).

## Grill Log

- **Q:** Why a Pudu program and not a shell script? **A:** The escaping rules are Pudu's own and
  are tested with Pudu; one language keeps the repository whole.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangMcp/Generated/Docs]]
