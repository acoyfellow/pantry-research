# E2: Cross-agent determinism

Status: ready (parallel with E1)
Unlocks: E3 (composition can trust shared recipes)
Needs: pantry (live) + >=2 distinct harnesses. No model required.

## Question

Does the SAME shared recipe produce BYTE-IDENTICAL output for the same input
when run by two or more DIFFERENT harnesses (not just two runs in one harness)?

## Hypothesis

A pure recipe fetched from pantry and run via the canonical runner is
harness-independent: the CLI/client path, the MCP server (over stdio), and a
plain Node/Worker invocation all yield identical output for the same input,
because they all run the same exact saved code in their own process.

## Method

1. Use a pure recipe (coordinate with E1's recipe, or push one). Fix an input set.
2. Run it through >=2 distinct harnesses:
   - the PantryClient / CLI path (`pantry get` + runRecipe);
   - the MCP server's `pantry_run` over stdio (a second, separate agent process);
   - optionally a third: a plain Node script or Worker invocation via PantryClient.
3. Collect each harness's output for each input.
4. Diff outputs across harnesses byte-for-byte.

## PASS criteria

- every harness ran the recipe without error;
- for each input, outputs are BYTE-IDENTICAL across all harnesses;
- the recipe code each harness fetched has the same hash (same artifact);
- receipt under receipts/ shows, side by side, each harness's output + the diff result.

## FAIL is a result

If harnesses differ, log WHY (runner differences, JSON serialization, locale,
Node vs Workers runtime semantics) — this defines which recipes are portable.

## Receipts

receipts/ — { recipe, codeHash, harnesses: [{name, outputs[]}], identicalAcrossHarnesses: bool }.
