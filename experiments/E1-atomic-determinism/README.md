# E1: Atomic determinism (the unit)

Status: ready
Unlocks: E2 (cross-agent determinism)
Needs: pantry (live) + one harness (this session). Zero model calls required.

## Question

If an agent fetches a recipe's exact saved code and runs it (instead of
re-deriving the logic), does it get the SAME output for the same input every
time — and did the agent avoid re-deriving the procedure?

## Hypothesis

A pure recipe (no I/O, no time, no randomness) fetched via `pantry get` and run
via the demo runner is deterministic: identical input yields byte-identical
output across repeated runs and across process restarts. The agent contributes
zero reasoning to the procedure itself — it only supplies input and runs saved code.

## Method

1. Author one pure recipe with a known oracle (candidate: a small deterministic
   transform with a non-trivial-but-pure body — e.g. `normalize_status` that maps
   a status line to a fixed JSON shape, or reuse `slugify`). Push it to pantry.
2. From this harness: `pantry get <name>` (capture the exact code), then run it
   over a fixed input set N times (and once in a fresh process).
3. Compare every run's output to the oracle and to each other.
4. Record that the agent ran saved code rather than writing the procedure inline.

## PASS criteria

- the recipe ran without error on every input;
- every run's output equals the oracle (functions performed right);
- repeated runs + a fresh-process run produce BYTE-IDENTICAL output (deterministic);
- the procedure logic came from the fetched recipe, not from agent reasoning
  (0 re-derivation — the agent supplied input only);
- a machine-verifiable receipt under receipts/ captures inputs, the fetched code
  hash, every output, and the determinism check.

## FAIL is also a result

If outputs differ across runs, log WHY (hidden I/O? time? Math.random? runner
nondeterminism?) — that tells us which recipes are safe to share and which are not.

## Receipts

receipts/ — to be filled by the run: { recipe, codeHash, inputs[], outputs[],
identicalAcrossRuns: bool, matchesOracle: bool, freshProcessIdentical: bool }.
