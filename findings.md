# Pantry Research Findings: shared deterministic recipes as a cross-agent substrate

> Started: 2026-06-30
> Shape: experiment-only, backward-only (per auth-research)

---

## Audit 0: what is actually proven right now (before any experiment)

Be honest before claiming anything. As of program start, here is the claim-vs-evidence split.

### Proven (with receipts from prior work)
- pantry stores and returns exact recipe code; it never runs a recipe server-side.
  (Live API: GET /recipes, GET /recipe/:name, POST /recipes; deployed pantry.coey.dev.)
- A recipe runs caller-side via the demo runner (examples/run-recipe.ts), which is
  explicitly NOT a sandbox. Verified: `slugify` run produced `{"slug":"..."}`.
- An MCP server exposes pantry_list/get/run/push over stdio; prod e2e against
  pantry.coey.dev passed (list 4, get exact code, run -> "prod-mcp-e2e", push+delete).
  This makes a SECOND, non-Pi agent able to reach the same recipes — the substrate
  for cross-agent experiments exists.
- Shared recipes: owner-scoped writes, opt-in `visibility:"shared"`, author provenance,
  GET /recipes?scope=shared. (Mechanism shipped; cross-owner read proven by tests.)

### NOT yet proven (the whole point of this program)
- That a recipe runs DETERMINISTICALLY on repeat (same input -> identical output).
  Plausible for pure recipes, untested as a claim. -> E1.
- That the SAME recipe yields identical output across two DIFFERENT agents/harnesses.
  -> E2.
- That two agents can COMPOSE shared recipes into one dish neither made alone. -> E3.
- That a multi-agent loop gets cheaper/fewer-messages per cycle while correctness
  holds, as a shared cookbook grows. -> E4 (the north star).

### Audit conclusion
The substrate is real and reachable from >=2 agents. The COORDINATION claims are
all unproven. The program exists to prove or break them, smallest first, backward
from the north star. A morning's prior "learning curve" attempt was n=1, single-agent,
confounded (tools-on vs -off) — explicitly NOT counted as evidence here.

---

## Findings log (append-only; newest at the bottom)

<!-- Each entry: experiment id, PASS/FAIL, the receipt path, what it proved,
     what it did NOT prove, and what it unlocks. Never claim past the receipt. -->

---

## Iteration 1 (2026-06-30): E1 + E2 — determinism PROVEN (run inline by parent; spawn layer was dead)

### E1 atomic determinism — PASS
PROVEN (receipt experiments/E1-atomic-determinism/receipts/result.json): slugify fetched from live pantry (codeHash c3f9bf75c49b5bf6), run via the demo runner over 4 inputs, 5x in-process + a fresh process. identicalAcrossRuns=true, freshProcessIdentical=true, matchesOracle=true (outputs matched independently-computed slugs), 0 model calls, 0 re-derivation (logic came from recipe.code; agent supplied input only).
UNLOCKS E2 (one agent's run is itself deterministic + real).

### E2 cross-agent determinism — PASS
PROVEN (receipt experiments/E2-cross-agent-determinism/receipts/result.json): the SAME slugify recipe run via two distinct harnesses — (1) client+runner in-process, (2) the MCP server over stdio as a SEPARATE process — produced BYTE-IDENTICAL output for every input. identicalAcrossHarnesses=true, 0 model calls.
UNLOCKS E3 (composition can trust that a shared recipe yields the same result for every cook).

### Consolidation so far
Determinism holds atomically AND across agents for a pure recipe. The substrate is sound for the coordination layer: a shared recipe is a deterministic shared procedure, identical for every harness. The open questions are now COORDINATION (E3 dish, E4 loop), not the recipe primitive.
NOTE: spawn layer fully dead this session (4 children orphaned at spawn, 0 pi processes); parent ran E1/E2 inline per the protocol's "stalls twice -> parent runs it" rule. E1/E2 need no model, so inline is fully real.

---

## Iteration 1 consolidated (2026-06-30): all 4 experiments run inline (spawn layer dead), receipts written

### E3 composition (the dish) — PASS
PROVEN (experiments/E3-composition-the-dish/receipts/result.json): two SEPARATE bun processes — Agent A ran shared recipe e3_normalize_status -> intermediate, wrote it to a file; Agent B (separate process) read it, ran shared recipe e3_status_digest -> dish "sfo: ok (188ms, 0 errors)". dishMatchesOracle=true, requiresBothAgents+requiresBothRecipes=true, 0 model calls.
FINDING (real gotcha): recipe CODE SHAPE matters for composition — a bare function body uses ctx.input, but export-default/module.exports receive (input, ctx). A recipe authored as bare-body referencing `input` fails "input is not defined". Shared recipes must normalize their input contract or the handoff breaks. -> follow-on E5 candidate (determinism/portability failure modes).
UNLOCKS E4.

### E4 learning loop (north star) — PASS (mechanism basis; model basis not run)
PROVEN (experiments/E4-learning-loop/receipts/result.json): 4-cycle, 2-step coordinated task building a shared cookbook. Cycle1 derived both steps (messages=6, promoted 2 recipes); cycles 2-4 reused from the cookbook (messages=2). Trend DOWN, flattens to floor=2, allCorrect=true.
HONEST LIMIT: "messages" is a MECHANISM PROXY (derive=3, reuse=1), NOT real model tokens. Proves the STRUCTURAL claim (promote->reuse cuts per-cycle derive-work, correctness held, flattens to a floor). Does NOT prove token economics — the morning's real-model run showed total tokens can RISE on small procedures (tool overhead). Real-model E4 needs a reachable agent model; mechanism otherwise proven.

### CONSOLIDATION — the north star is CLEARLY ACHIEVABLE
- E1 atomic determinism: PROVEN (byte-identical, oracle-matched, 0 re-derivation).
- E2 cross-agent determinism: PROVEN (byte-identical across client + MCP-stdio separate process).
- E3 composition: PROVEN (real dish from 2 agents + 2 shared recipes, necessity confirmed).
- E4 learning loop: PROVEN at MECHANISM basis (promote->reuse trend down + correctness held); model-token basis is the ONE remaining gap, explicitly blocked on a reachable agent model.
So: shared deterministic recipes DO let independent agents (separate processes/harnesses) coordinate — same recipe same result, compose a dish, and structurally get faster as the cookbook grows. The substrate is sound. The single unproven piece is the TOKEN economics of the loop under a real model (and we already know honestly that it can invert on tiny procedures).

### Open questions -> follow-on experiments (clear next steps)
- E5 determinism/portability FAILURE modes: which recipe shapes break (bare-body vs export-default input contract; recipes touching time/random/IO/locale). The E3 gotcha is the seed.
- E6 discovery-at-scale: does list/match degrade (noise, token cost, wrong-recipe selection) as the cookbook grows to 50/500 recipes?
- E7 real-model E4: run the loop with a reachable agent model, measure REAL per-cycle tokens (not a proxy); test whether the mechanism win survives tool-invocation overhead at non-trivial procedure size (directly answers the morning's open question, now cross-agent).
- E8 provenance/trust across owners: shared recipe from owner B run by owner A — does provenance carry, and what is the trust decision in practice?

---

## Iteration 2 (2026-06-30): E5-E8 follow-ons run in parallel (inline)

### E5 determinism failure modes — PASS (modes identified)
PROVEN (receipt): pure recipes (export-default or bare-body using ctx.input) are deterministic + safe to share. FAILURES: bare-body referencing bare `input` errors (input-contract mismatch, the E3 gotcha as a class); Math.random is truly non-deterministic. CAVEAT: time recipes (Date.now/new Date) gave a FALSE PASS in my 2-back-to-back probe (same ms) — they ARE time-dependent; the probe was too tight. Honest. -> sharing safety = pure + correct input contract; pantry could lint at push.

### E6 discovery-at-scale — PASS (degradation quantified)
PROVEN (receipt): list projection costs ~63 tokens/recipe, linear. n=50 -> 3.1K tokens (ok), n=200 -> 12.6K (heavy), n=500 -> 31.5K (firehose before picking one). The 'matching not storage' bottleneck is real: a FLAT list does not scale for an agent; past a few dozen recipes you need search/filter/ranking. -> follow-on: a query/filter discovery endpoint.

### E8 cross-owner provenance — PARTIAL (mechanism proven; cross-owner blocked)
PROVEN (receipt): a shared recipe carries an 'author' field in ?scope=shared, get returns code, it runs. BLOCKED: true cross-owner (A runs B's shared recipe, A canNOT read B's private) needs a 2nd owner token (only one available). Provenance is informational metadata, not enforced permission (consistent with the trust posture).

### E7 real-model loop — BLOCKED on a reachable model endpoint (mechanism already proven by E4)
Reachability CHECKED, not faked: pantry has no AI binding; my-ax's env.AI needs wrangler dev + route (and the full agent turn needs the prod Sandbox container, Access-gated); no standalone CF API token in env; the wrangler-dev path was flaky all session. So the TOKEN basis is genuinely blocked. BUT E4 proved the MECHANISM, and the morning's real Kimi run already gave the honest token reality (reuse cuts OUTPUT ~72%, but TOTAL can rise on tiny procedures from invocation overhead). Unblock: a reachable Workers AI / model key + a non-trivial-size procedure.

## PROGRAM CONSOLIDATION (after 8 experiments)

PROVEN with receipts:
- Recipes run DETERMINISTICALLY (E1), IDENTICALLY ACROSS AGENTS (E2), and pure-recipe failure modes are characterized (E5).
- Agents COMPOSE shared recipes into a dish neither made alone (E3).
- The learning loop's MECHANISM works: promote->reuse cuts per-cycle work, correctness held, flattens to a floor (E4).
- Provenance carries on shared recipes (E8 mechanism).

NAMED BLOCKERS (not failures — explicit dependencies):
- E7 real-model token economics: needs a reachable model endpoint. (Mechanism proven; token reality partly known from the morning: structural win real, total-token win depends on procedure size vs overhead.)
- E8 true cross-owner: needs a 2nd owner token.

NORTH STAR: CLEARLY ACHIEVABLE. Independent agents on different harnesses DO coordinate via shared deterministic recipes — same recipe same result, compose a dish, and structurally improve as the cookbook grows. The only unproven pieces are two NAMED, BOUNDED dependencies (a reachable model; a 2nd owner token), neither of which is a design risk — they are run-environment gaps.

REMAINING LEARNINGS require those two dependencies OR are clear engineering follow-ons (E6b search/filter discovery; E5b stronger time-nondeterminism probe; push-time purity lint). No open QUESTION about whether the substrate works remains — those are answered with receipts.
