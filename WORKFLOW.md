# Workflow

How the build is sequenced and how the lab is used day to day. The guiding
principle: small stages, each ending in a verified result, with a clear
must-finish core separated from stretch goals.

## Build sequence

Stages run in parallel where they have no dependency, rather than strictly
one-after-another, so idle waiting (e.g. a long data-collection window) never
blocks hands-on work.

1. **Inference node** — install and smoke-test the candidate models; confirm the
   API requires authentication; leave production settings reversible.
2. **Workstation** — build the typed tool hub, the disposable sandbox runner,
   and the evaluation harness. These depend on neither the inference node nor
   the hub node and can be built and unit-tested in isolation (against a mock
   model) first.
3. **Tournament** — run the weighted suite across the candidate models; pick the
   heavy and light models by category, not by a single overall score.
4. **Hub node** — stand up headless; deploy the hardened sandbox; prove the
   isolation boundary with escape tests.
5. **Tool layer** — add validated tools and run the injection corpus against
   them; apply and measure the defense layer.
6. **Real workload** — use the lab to do actual security research, as the proof
   the infrastructure is useful rather than ornamental.

## Must-finish core vs. stretch

Defining this boundary up front keeps the outcome from depending on every phase
landing.

**Core (must finish):** tool hub · disposable sandbox · evaluation harness ·
injection corpus + measured defense · the capability-vs-safety comparison.

**Stretch (bonus only):** automatic task routing · light-model triage ·
experimental hardware acceleration · dashboards.

## Handoff discipline (multi-machine)

- Each machine's setup ends in a short handoff note: endpoints, aliases,
  recommended settings, known limitations, and the exact commands to bring it
  up and down.
- Secrets (API keys) are transferred out of band and never committed or pasted
  into shared logs.
- One change at a time: measure before and after, back up configuration first,
  and keep system-level changes reversible.

## Daily use once built

- **Hard coding / debugging:** strong planner agents on the workstation, with
  private or bulk work delegated to the heavy local model.
- **Anything that executes code:** only through the sandbox.
- **Model or config changes:** re-run the evaluation first; keep a change only
  if the numbers improve.
- **Offline / travel:** the light model plus the heavy local model over the
  private mesh, no cloud dependency.
