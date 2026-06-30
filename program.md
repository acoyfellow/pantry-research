# Program: Shared deterministic recipes as a cross-agent coordination substrate

> Started: 2026-06-30
> Shape: experiment-only, backward-only (per auth-research)
> Substrate under test: pantry (https://pantry.coey.dev) — stores recipes, never runs them

## The question the whole program asks

Can independent agents on different harnesses coordinate by sharing deterministic
recipes — each running the same code in its own isolate, getting the same result,
composing outcomes — and measurably improve together as they build a shared cookbook?

If yes, pantry is the multiplayer substrate Cloudflare's single-agent runtimes
(Think/codemode) do not own. If no, we learn exactly where it breaks (determinism,
discovery, trust, or coordination) — also a real result.

## Working BACKWARD from the north star

The north star is E4. Each experiment is the smallest test that unlocks the next.
We build the ladder from the top, then execute from the bottom.

### E4 — The learning loop (the north star itself)
Two+ agents run a repeating multi-step task, sharing a cookbook, promoting new
shared recipes as they find reusable steps. MEASURE messages/cost per cycle
trending down while correctness holds 100%. This is "they got better at cooking
together." PASS = a real downward curve across cycles with correctness held,
cross-agent, with receipts.

### E3 — Composition (the dish)
A recipe that orchestrates other recipes: agent A runs recipe X, passes output to
agent B who runs recipe Y, producing a composed artifact neither made alone.
PASS = a "dish" exists that required >=2 shared recipes + >=2 agents, with receipts
of each step and the final artifact. (Unlocks E4: you cannot measure a loop getting
better until a single coordinated pass works.)

### E2 — Cross-agent determinism
The SAME recipe run by two harnesses (e.g. this Pi session + a second agent via the
MCP server / client) yields BYTE-IDENTICAL output. PASS = same input -> same output
across >=2 distinct agents/harnesses, receipts side by side. (Unlocks E3: composition
is only safe if shared recipes are deterministic across cooks.)

### E1 — Atomic determinism (the unit) — START HERE
Author + push ONE pure, deterministic recipe; an agent RUNS it (via pantry get+run)
instead of re-deriving the logic; prove same-input -> same-output across repeated
runs. PASS = the recipe ran, passed its oracle, and produced identical output on
repeat, with receipts, and the agent did zero re-derivation of the logic.
(Unlocks E2: cross-agent identity is meaningless until one agent's run is itself
deterministic and real.)

## The honest dependency chain

E1 needs only pantry + one harness (this session) — fully runnable now, zero model
calls required for the determinism claim.
E2 needs a SECOND reachable agent/harness (the MCP server makes this possible; or a
second Pi/Worker). Runnable now.
E3 needs >=2 agents that can hand off — coordination plumbing. The first place a
model/harness reachability constraint may bite; be explicit.
E4 needs E3 repeated with promotion + per-cycle metrics — the morning's learning-curve
idea, but cross-agent and real. The hardest; likely needs reachable models.

## What a PASS looks like (program-wide)

Every experiment ends with a receipt under its experiments/NN-name/receipts/ that a
skeptic can re-run or inspect: real inputs, real outputs, the metric, and an honest
note on what was proven vs. what needs a reachable model/harness.

## Non-goals

- Not building a product. Not a marketplace. Not a runtime. Not competing with Think.
- Not claiming token savings (the red team killed that; reuse-of-exact-code is the
  honest mechanism — determinism + correctness, not fewer total tokens).
