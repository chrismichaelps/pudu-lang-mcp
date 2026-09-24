---
type: module
path: "@root/src/Services/Process/Bounded.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
seam: "[[seams/Toolchain]]"
depth_score: 0.7
depth_status: DEEP
coupling: 1
interface_stability: 0.9
tags: [module, deep, backbone]
aliases: [Bounded Process]
---

# Bounded process

## Purpose

Run one program with input, a working directory, a deadline, and an output cap, and always come
back with what it wrote.

## Interface

### Signatures

```pudu
export type Finished = { status: Int, output: Str, errors: Str, timedOut: Bool, truncated: Bool, millis: Int }

export fn run(program: Str, arguments: &Array[Str], input: Str, directory: Str, millis: Int, capBytes: Int) -> Result[Finished, Str]
export fn textWithin(written: &Bytes, limit: Int) -> (Str, Bool)
export fn deadlineReached(now: Int, deadline: Int) -> Bool
```

### Governance

- `Err` means the program could not be started or waiting failed; a program that ran and exited
  nonzero is `Ok` with its status. A failed wait stops the child and joins both readers first.
- A timed-out program is stopped and answers `timedOut: true`, `status: -1`, and its partial output.

### Linkage

- **Requires:** `Std.Process`, `Std.Concurrent`, `Std.Sync`, `Std.Bytes`, `Std.Env`.
- **Consumed by:** [[src/Services/Toolchain]].

## Algorithm

1. Launch with the directory set (empty directory: inherit), and start it.
2. Start one reader thread per stream before writing input, so a child that answers while it is
   still being written to never blocks on a full pipe. Each reader keeps at most `capBytes + 1`
   bytes and keeps draining past that.
3. Write the input, close the child's input.
4. Poll `waitWithin` every 25 ms until the child exits or the deadline passes; past it, stop the child.
5. Join both readers; decode each stream with `textWithin`.

`textWithin` keeps at most `limit` bytes and backs off up to three bytes so a cut never splits a
UTF-8 character; bytes that are not text at all answer a fixed marker. A cut that keeps only part
of the first character answers empty text, marked as cut.
The reader retains the first `capBytes + 1` bytes and drains the rest. A stream read error ends
that reader and preserves its partial bytes; a stopped child can close a pipe during a read.

## Negative Logic (Prohibited Paths)

- No unbounded read into memory; no wait without a deadline.

## Edge Cases

- `millis <= 0` stops the child at the first poll.
- A reading exactly at the deadline has expired; invalid bytes shorter than a character are
  distinct from a truncated character.

## Depth

DEPTH 0.7 (DEEP). A small signature over threads, pipes, deadlines, and UTF-8 boundaries.

## Grill Log

- **Q:** Why not `Process.within`? **A:** It takes no input; every compiler conversation here
  writes to the child's input. _Rejected:_ writing source to a file for every call.
- **Q:** How are wait failures and exact deadlines distinguished from timeouts? **A:** Propagate
  wait errors after stopping and joining the child; test the deadline comparison directly with
  readings before, at, and after it. _Rejected:_ treating wait failure as a timeout.
- **Q:** Why keep draining after the byte cap or a read error? **A:** Draining avoids blocking a
  child whose output exceeds the cap; an actual read error terminates that stream and preserves
  the bytes already captured. _Rejected:_ returning early with a live child.

## Referenced by

[[src/Services/Process/_MOC]] · [[seams/Toolchain]]
