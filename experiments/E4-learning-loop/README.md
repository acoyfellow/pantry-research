# E4: The learning loop (the north star)

Status: ready (parallel; the metric is only meaningful once E3 produces a dish)
Unlocks: the north star itself
Needs: pantry (live) + >=2 agents + a repeating task. The cost/messages trend is
mechanism-measurable; a REAL model makes the trend a token trend. Be explicit
which ran. NEVER fabricate a downward curve.

## Question

As two+ agents repeat a coordinated multi-step task and promote new SHARED
recipes for the reusable steps, do the messages/turns per cycle go DOWN while
correctness stays 100% — i.e. do they get better at cooking together?

## Hypothesis

Cycle 1, agents derive each step (high messages/turns). When a step recurs, it is
promoted to a shared recipe; later cycles fetch+run the recipe instead of
re-deriving, so messages/turns per cycle fall and flatten to a floor (discovery +
the genuinely novel part still cost), while correctness holds because the recipe
is deterministic (E1/E2).

## Method

1. A repeating task family with a stable, checkable outcome. Run it across N
   cycles with >=2 agents (separate processes; pi children / MCP clients / Workers).
2. Per cycle: record messages/turns to complete, which steps used a shared recipe
   (recipe.run) vs were derived, which steps got PROMOTED to a new shared recipe,
   and correctness vs the oracle.
3. Plot messages/turns per cycle and correctness per cycle.
4. HONESTY: the deterministic mechanism (promote -> reuse -> fewer derive steps)
   is fully measurable without a model. If a real model drives the agents, also
   record real per-cycle token usage and label it. If no model is reachable,
   measure the MECHANISM (derive-vs-reuse step counts, messages) for real and
   label the token trend as not-run. Do NOT invent a curve.

## PASS criteria

- a real multi-cycle run happened (>=3 cycles, >=2 agents);
- shared recipes were promoted from reusable steps during the run;
- messages/turns per cycle show a real DOWNWARD trend, flattening to a floor;
- correctness held (matches oracle every cycle);
- receipts under receipts/ capture per-cycle metrics + the promotions + the
  honest label of mechanism-measured vs model-measured.

## FAIL / partial is a result

If the trend is flat or up, log WHY (coordination overhead exceeds reuse savings —
the same honest finding the morning's single-agent run surfaced; now test it
cross-agent). If no model reachable, a labeled mechanism-only partial is the result.

## Receipts

receipts/ — { cycles: [{n, messages, stepsDerived, stepsReused, recipesPromoted, correct}], trend: "down|flat|up", basis: "mechanism|model", modelReachable: bool }.
