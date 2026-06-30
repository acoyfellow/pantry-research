# E3: Composition (the dish)

Status: ready (parallel; consolidation depends on E1/E2 determinism holding)
Unlocks: E4 (a loop is only meaningful once one coordinated pass works)
Needs: pantry (live) + >=2 separate agent processes. No model strictly required
for the mechanism; a model makes the "agents" autonomous rather than scripted.

## Question

Can two separate agents (distinct processes/harnesses), each running a SHARED
recipe, hand results to each other and together produce ONE artifact that
neither produced alone — a "dish"?

## Hypothesis

A dish = recipe composition across agents. Agent A runs shared recipe X over an
input, produces an intermediate; Agent B runs shared recipe Y over A's
intermediate, produces the final artifact. Because X and Y are shared
deterministic recipes (E1/E2), the dish is reproducible and the handoff is clean.

## Method

1. Define a small two-step task with a checkable final artifact (e.g. step X
   normalizes/transforms input -> intermediate; step Y validates/renders the
   intermediate -> final). Author X and Y as shared recipes; push them.
2. Run two SEPARATE agent processes (pi child processes, MCP clients, or Worker
   invocations — NOT my-ax; avoid the prod-container dependency):
   - Agent A: pantry get+run X over the input -> intermediate (receipt).
   - Agent B: receives A's intermediate, pantry get+run Y -> final dish (receipt).
3. Verify the final dish against an oracle and confirm it required BOTH agents +
   BOTH shared recipes (remove either and it cannot be produced).

## PASS criteria

- two distinct agent processes each ran a shared recipe;
- A's output fed B's recipe (real handoff, captured);
- the final dish matches its oracle;
- the dish provably required >=2 shared recipes + >=2 agents (necessity check);
- receipts under receipts/ capture each step's input/output + the final artifact.

## FAIL is a result

If the handoff is brittle (serialization, schema mismatch, who-owns-the-recipe),
log it — that is the coordination-layer learning the program exists to find.

## Receipts

receipts/ — { recipeX, recipeY, agentA: {input, intermediate}, agentB: {intermediate, dish}, dishMatchesOracle: bool, requiresBothAgents: bool }.
