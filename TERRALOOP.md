# Pantry-Research Parent Terraloop — orchestrate 4 experiments IN PARALLEL

## Shape (the new mode)
Experiment-only. Backward-only. PARALLEL, not sequential. The PARENT terraloop
orchestrates FOUR child terraloops at once — one per experiment (E1, E2, E3, E4)
— then consolidates results, re-steers, spawns FOLLOW-ON experiments when there
is a clear next step, and only stops when learnings are exhausted AND the north
star is clearly achievable. Read program.md, LOOP.md, findings.md, status.json.

## North Star (binary)
Agents on different harnesses coordinate via shared deterministic recipes (same
recipe, same result, each in its own isolate), compose a dish none made alone,
and get faster together as a shared cookbook grows while correctness holds 100%.

## The 4 parallel children (each its own terraloop)
Do NOT gate E2/E3/E4 behind E1 finishing. Run all four NOW, in parallel. Each is
an isolated experiment that produces machine-verifiable receipts under
experiments/EN-*/receipts/ and appends an honest finding. Each child works
backward from its own PASS criteria (see experiments/EN-*/README.md).

- E1 atomic determinism — one pure recipe runs, passes oracle, identical on repeat,
  0 re-derivation. Substrate: pantry live + the demo runner. No model needed.
- E2 cross-agent determinism — same recipe, BYTE-IDENTICAL output across >=2
  distinct harnesses (e.g. the CLI/client path AND the MCP server over stdio AND a
  plain Node/Worker invocation). Substrate: pantry live + MCP + client. No model needed.
- E3 composition (the dish) — >=2 agents (pi child processes / MCP clients / Worker
  invocations) each run a shared recipe; A's output feeds B's recipe; together they
  produce ONE artifact neither made alone. Capture each step + the final dish as receipts.
  "Agents" are real separate processes, NOT my-ax (avoid the prod-container dependency).
- E4 learning loop (north star) — repeat a multi-step coordinated task across cycles;
  promote new shared recipes as reusable steps appear; MEASURE messages/turns + correctness
  per cycle. Show the trend HONESTLY: if a real model is reachable use it; if not, drive the
  deterministic-mechanism part for real and LABEL what is mechanism vs model. NEVER fabricate
  a downward curve (the morning's n=1 confound is explicitly not evidence).

## Honesty bar (non-negotiable)
A demo is not proof; receipts are. Every child separates PROVEN (with a receipt) from
CLAIMED. No fabricated metrics or curves. If a model/harness is unreachable, run the real
part and say which. No token-savings claims. Determinism (E1/E2) is provable with ZERO
model calls — those must be fully real. E3/E4 may be partial+labeled if model-gated.

## Parent responsibilities each tick
1. Status all 4 children (TEXT). NEEDS-ATTENTION is often a long real run — check
   mtimes/process/receipts landing. If a child runs `find /` KILL it + respawn leaner.
   If a child stalls at spawn (supervisor alive, no pi child, mre 0 bytes >6min) reap +
   respawn; if it stalls twice, the PARENT runs that experiment directly (the spawn layer
   has been flaky — determinism experiments are runnable inline by the parent).
2. On a child finishing: VERIFY ITS RECEIPT YOURSELF (re-read/re-run the receipt; confirm
   it proves what it claims; reject demos-as-proof). Append the honest finding to findings.md;
   update status.json proven/notProven.
3. CONSOLIDATE across children: what did the set teach? Did E1/E2 prove determinism so E3 can
   trust shared recipes? Did E3 produce a real dish so E4's loop is meaningful? Re-steer: if a
   child's PASS criteria were wrong or its result reveals a sharper question, REWRITE that
   experiment and re-run it.
4. SPAWN FOLLOW-ON experiments when there is a clear next step (E5, E6, ...): e.g. determinism
   FAILURE modes (which recipe shapes are non-deterministic and why), discovery-at-scale,
   provenance/trust across owners, the bottlenecks ranked earlier (naming collision, staleness).
   Each follow-on is its own child terraloop in the same shape.

## Stop gate (when to delete this loop)
Stop ONLY when BOTH: (a) no remaining experiment has a learning we can still extract (every open
question is either answered-with-a-receipt or explicitly blocked on an unreachable model/harness
with that blocker named), AND (b) the north star is CLEARLY ACHIEVABLE — i.e. E1+E2 proven, E3
produced a real dish, and E4 either showed the real trend or is reduced to "needs reachable model X,
mechanism otherwise proven." At stop: write a consolidated findings summary (what's proven, what's
the clear path to the north star, what each follow-on would test) and report to Jordan.

## Safety
Never fabricate receipts/metrics/curves. Never commit secrets/PANTRY_TOKEN; .context/.dev.vars/.guardrail
out of repos (guardrail blocks .context in a deployable repo — pantry-research is research, keep it out
of the pantry deploy path). Clean any throwaway recipes from prod (leave the original 4). Lean on
Think/Pi/OpenCode/Workers as cooks; do not compete with the runtime. No find /.
