# Distributed Private AI Lab — Concept & Method

A design and methodology write-up for building a small, self-hosted,
multi-machine AI development environment that treats local language models
as **tools driven by stronger planners**, runs untrusted code in disposable
sandboxes, and **measures every model and defense choice empirically** instead
of trusting vendor claims.

This repository documents the *ideology, architecture, workflow, and
evaluation method*. It intentionally does **not** contain the implementation,
infrastructure details, or any host-specific configuration.

## The core idea

Most "local AI" setups stop at "I ran a model on my machine." This design goes
further with three principles:

1. **Planners plan; local models are tools.** The strongest available models
   (cloud or otherwise) do the reasoning and orchestration. Smaller local
   models are called as tools for private, cheap, or bulk work — never put in
   charge of routing or privileged actions. The weakest brain should not be the
   one deciding what to delegate.

2. **Nothing untrusted runs on a machine that matters.** Any model-generated or
   untrusted code executes in a disposable, network-isolated, capability-dropped
   container that is destroyed after use. The boundary is *tested*, not assumed.

3. **Claims are benchmarked, not believed.** Model selection, context-length
   choices, quantization, and even security defenses are decided by measured
   results on a task suite that mirrors real use — with predictions written down
   *before* the run.

## Why it's structured this way

- **Security as the thesis, not a feature.** The headline isn't "I wired tools
  together" — it's "I built a tool-using agent platform and then attacked it,
  measured how it broke, and fixed it."
- **A real workload proves the lab.** The infrastructure earns its keep by
  enabling actual security research, not by existing for its own sake.
- **Reproducible and honest.** Pinned dependencies, versioned benchmark data,
  pre-registered predictions, and limitations stated plainly.

See [ARCHITECTURE.md](ARCHITECTURE.md), [METHODOLOGY.md](METHODOLOGY.md), and
[WORKFLOW.md](WORKFLOW.md).

## Status

Concept and method, actively being implemented privately. This repo is the
public-facing design record.
