---
type: module
path: "@root/src/Domain/Docs/Chapter.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.55
depth_status: MODERATE
coupling: 2
interface_stability: 0.85
tags: [module]
aliases: [Chapter, Section]
---

# Chapter

## Purpose

The published text's shape: documents (chapters, site pages, release notes, and example programs) as written, sections cut at second-level headings, anchors
for addressing a section, and the table of contents.

## Interface

### Signatures

```pudu
export type Chapter = { group: Str, slug: Str, title: Str, markdown: Str }   // group: docs | pages | releases | examples
export type Section = { group: Str, chapter: Str, chapterTitle: Str, heading: Str, anchor: Str, text: Str }

export fn sectionsOf(chapter: &Chapter) -> Array[Section]
export fn allSections(chapters: &Array[Chapter]) -> Array[Section]
export fn anchorOf(heading: Str) -> Str
export fn find(chapters: &Array[Chapter], group: Str, slug: Str) -> Option[Chapter]
export fn inGroup(chapters: &Array[Chapter], group: Str) -> Array[Chapter]
export fn sectionIn(chapter: &Chapter, wanted: Str) -> Option[Section]
export fn tableOfContents(chapters: &Array[Chapter]) -> Str
```

### Linkage

- **Requires:** `Std.List`, `Std.Text`.
- **Consumed by:** [[src/Generated/Docs]], [[src/Domain/Docs/Search]], [[src/App/Tools/Docs]], [[src/App/Resources]].

## Algorithm

- `sectionsOf`: walk lines, tracking fenced code blocks (lines starting with three backticks). Outside
  a fence, a line starting `# ` is the document's title and is skipped, and a line starting `## ` starts a section named by the rest of the line. Text before the
  first such line is the introduction section, headed by the chapter title. Empty sections are kept
  only if they have a heading line of their own.
- `anchorOf`: lowercase; the words left after every character other than an ASCII letter or digit
  becomes a space, joined by `-`.
- `sectionIn`: match `wanted` against a section's anchor or its heading, case-insensitively.
- `find` matches group and slug together; `inGroup` keeps corpus order.
- `tableOfContents`: one line per chapter, `group/slug — title`, with its section headings indented.

## Negative Logic (Prohibited Paths)

- A `## ` line inside a code fence never starts a section.

## Edge Cases

- A chapter with no `## ` heading is one section.

## Depth

DEPTH 0.55.

## Grill Log

- **Q:** Cut at `###` as well? **A:** No; third-level headings are short and read best with their
  parent section. _Rejected:_ finer sections that lose their context.

## Referenced by

[[src/Domain/Docs/_MOC]]
