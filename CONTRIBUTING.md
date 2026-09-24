# Contributing to pudu-lang-mcp

The wiki vault under `wiki/` is the source of truth. Read `wiki/00-INDEX.md`, the architecture
map, and the grammar page before changing code, and write or update a module's mirrored page under
`wiki/src/` before its code.

## Branches

- `main` holds released versions only. It changes through a pull request from `dev`.
- `dev` is the integration branch.
- Work happens on `feature/<issue>-<slug>`, `fix/<issue>-<slug>`, or `docs/<issue>-<slug>`,
  branched from `dev` and merged back through a pull request.

## Commits

Semantic commits that name the issue: `feat(tools): add rename preview refs #12`. Types are
`feat`, `fix`, `perf`, `refactor`, `test`, `docs`, `ci`, and `chore`. Keep each commit to one
change; code, tests, and the matching wiki pages move together.

## Code

- Dependencies point inward: `Main`, `App`, `Services`, `Domain`, then `Utils`, `Constants`, and
  `Errors`. `Domain` performs no effects.
- Every file and exported type starts with a one-line `/** @Namespace.Entity.Role — intent */`
  anchor, and every function and constant carries a short `///` comment saying what it answers.
- No dead code: every declaration has a caller.
- Keep files under 500 lines.

## Checks

Every change passes all of these:

```bash
pudu check $(find src test tools -name '*.pudu')
pudu fmt --check src test tools
pudu lint src test tools
pudu test test
```

Changes to logic also run mutation testing on the files they touch, and no new mutant may survive
without a written reason:

```bash
pudu run tools/Mutate.pudu --file src/PuduLangMcp/Domain/Protocol/Pagination.pudu
```

The documentation corpus is regenerated from a pudu-lang checkout, never edited by hand:

```bash
pudu run tools/SyncDocs.pudu ../pudu-lang "$(git -C ../pudu-lang rev-parse --short HEAD)"
```

## Pull requests

Open the pull request against `dev`, describe the behaviour and how it was verified, and link the
issue with `Closes #N`.
