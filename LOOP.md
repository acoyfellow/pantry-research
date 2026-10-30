# Pantry Research-Experiment Loop

Read program.md for the north star. Read findings.md for the findings log.
Read status.json for current state. Experiments live in experiments/.

This is a RESEARCH program, not a product. The unit of work is an EXPERIMENT,
not a feature. We work BACKWARD from the north star: each experiment is the
smallest falsifiable test that, if it passes, makes the next one possible.

## The North Star (binary, falsifiable)

A group of agents on different harnesses (Pi, Think, OpenCode, a plain Worker)
coordinate through shared deterministic recipes: each runs the SAME recipe in
its OWN isolate, gets the SAME result, passes results to each other, and
together produce an outcome none made alone — and as they accumulate a shared
cookbook, the messages/turns per cycle go DOWN while correctness stays 100%.

Said plainly: **shared recipes let independent agents cook one dish, and they
get faster at it together without re-deriving or disagreeing.**

## Why this is NOT fragmenting the Cloudflare agents story

Think/codemode make ONE agent capable (runtime-local tools + snippets, executed
in that agent). That is depth inside a single agent. This program studies the
layer BETWEEN agents: a shared, deterministic, runnable procedure two+ agents
both run so they can coordinate. Think makes a cook; pantry is the shared shelf
a kitchen of cooks works from. The experiments lean ON Think as a cook, never
compete with it. If a result reads as "competing with the runtime," it is the
wrong experiment.

## What to optimize for (the metrics every experiment reports)

1. messages/turns to accomplish the task (lower is better)
2. functions performed RIGHT (a recipe ran and passed its oracle)
3. recipes that run DETERMINISTICALLY (same input -> same output, every agent)
4. cross-agent orchestration (>=2 agents share recipes, hand off, co-produce)

## The metaphor (the generative frame)

- recipes = deterministic shared procedures (pantry stores, never runs)
- agents = the cooks (Think / Pi / OpenCode / a Worker — any harness)
- a dish = a composed outcome multiple agents produce by each running shared recipes
- the kitchen = each agent's own isolate (execution stays local; that is what keeps it cross-harness)

## Loop rules

Each iteration:
1. Read status.json for what's next.
2. Pick the SMALLEST experiment that moves toward the north star (work backward).
3. Write the experiment as: Question -> Hypothesis -> PASS criteria -> receipts.
4. Run it for real. Capture machine-verifiable receipts (real outputs, not claims).
5. PASS or FAIL — both are results. Log WHY either way.
6. Append an honest finding to findings.md (claim vs evidence; audit yourself).
7. Update status.json.
8. Commit.
9. Start the next experiment.

## Honesty bar (non-negotiable, copied from auth-research)

A demo is not proof. Receipts are proof. Every finding separates what is PROVEN
(with a receipt) from what is CLAIMED. Never fabricate a result. If an
experiment needs a model/harness that is not reachable, say so and run the part
that is real. The deterministic core (recipes run identically) is provable with
zero model calls; the cross-agent loop needs reachable agents — be explicit
about which ran.

## DO NOT

- Build product features. This is experiments only.
- Run experiments forward (do not "add a thing then see"). Work backward from
  the north star: name the end state, then the smallest test that unlocks it.
- Compete with Think/codemode. Lean on them as cooks.
- Fabricate a receipt or a metric.
- Let an experiment claim more than its receipt proves.

## Paths

- pantry-research: ../pantry-research
- pantry (the substrate under test): ../pantry (live pantry.coey.dev)
- Think (a cook; reference, not target): @cloudflare/think in a sibling project node_modules
