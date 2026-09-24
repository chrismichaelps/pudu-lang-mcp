---
type: module
path: "@root/src/PuduLangMcp/Services/Process/Bounded.pudu"
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
  nonzero is `Ok` with its status. A failed wait stops the child and settles both readers first.
- A timed-out program is stopped and answers `timedOut: true`, `status: -1`, and its partial output.
- Once the child has exited or been stopped, its readers have 250 ms to reach the end of their
  streams. A stream still open then, held by a process the child started, answers what was read
  and is marked `truncated`; its reader is left to finish on its own.

### Linkage

- **Requires:** `Std.Process`, `Std.Concurrent`, `Std.Sync`, `Std.Bytes`, `Std.Env`.
- **Consumed by:** [[src/PuduLangMcp/Services/Toolchain]].

## Algorithm

1. Launch with the directory set (empty directory: inherit), and start it.
2. Start one reader thread per stream before writing input, so a child that answers while it is
   still being written to never blocks on a full pipe. Each reader keeps at most `capBytes + 1`
   bytes and keeps draining past that, publishing what it holds after every piece and marking the
   stream ended when it closes.
3. Write the input, close the child's input.
4. Poll `waitWithin` every 25 ms until the child exits or the deadline passes; past it, stop the child.
5. Poll both readers every 2 ms until each stream has ended or 250 ms have passed since the child
   ended; join the ended readers. Decode each stream with `textWithin`.

`textWithin` keeps at most `limit` bytes and backs off up to three bytes so a cut never splits a
UTF-8 character; bytes that are not text at all answer a fixed marker. A cut that keeps only part
of the first character answers empty text, marked as cut.
The reader retains the first `capBytes + 1` bytes and drains the rest. A stream read error ends
that reader and preserves its partial bytes; a stopped child can close a pipe during a read.

## Negative Logic (Prohibited Paths)

- No unbounded read into memory; no wait without a deadline, including the wait for a reader.

## Edge Cases

- `millis <= 0` stops the child at the first poll.
- A grandchild that inherits the output pipes and outlives the child (a shell that forks rather
  than replacing itself, a daemon) keeps the streams open; the run still ends within the deadline
  plus the reader grace.
- A stream kept open by a grandchild that closes it within the grace is read to its end and is not
  marked cut; one stream held open past the grace marks the run cut even when the other closed.
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
- **Q:** What bounds the wait for the readers after the child is gone? **A:** A fixed 250 ms grace
  measured from the child's end, polled against a flag each reader sets when its stream closes; a
  reader still blocked then is abandoned and its stream marked cut. Output already written by the
  child sits in the pipe and is read well inside the grace. _Rejected:_ joining unconditionally,
  which waits for every process holding the pipe (5 s for `sh -c "sleep 5; true"` stopped at
  200 ms); waiting until the original deadline, which holds a finished call for its whole budget.
- **Q:** What does a reader cell that cannot be read answer? **A:** A stream still open, so the
  wait ends at the grace rather than joining a reader that may never finish. `Sync.get` fails only
  on a cell that is gone, which no test can arrange; the mutant flipping that fallback survives for
  that reason alone. _Rejected:_ an ended stream, which would join without a bound.

## Referenced by

[[src/PuduLangMcp/Services/Process/_MOC]] · [[seams/Toolchain]]
