# Architecture

A three-node design, each machine given the role that fits its strengths,
connected over a private encrypted mesh.

```
                    private mesh (WireGuard-based, e.g. Tailscale)
 ┌───────────── Workstation (cockpit) ────────────┐
 │ editor + strong planner agents                 │
 │ evaluation harness · control CLI · light model │
 └───────────────┬─────────────────────────────────┘
                 │ MCP (typed, validated tools)
 ┌───────────────▼─────────────────────────────────┐      ┌──── Inference node ────┐
 │ Hub node (headless)                             │ ◄──► │ heavy local model       │
 │ MCP gateway · policy/router · audit log         │      │ OpenAI-compatible API   │
 │ disposable sandbox · scan lane                  │      │ one model resident      │
 └─────────────────────────────────────────────────┘      └─────────────────────────┘
```

## Node roles

| Node | Role | Rationale |
|---|---|---|
| **Workstation** | Where you work. Strong planner agents, the evaluation harness, the control CLI, and (optionally) a fast light model on a local GPU. | Most powerful machine; always-on during working hours. |
| **Inference node** | Serves the heavy local model over an OpenAI-compatible API, one model resident at a time. Has a reversible "server mode / normal mode" switch. | A larger model needs the unified memory a laptop-class machine can give it; kept focused on inference only. |
| **Hub node (headless)** | Always-on tool host: MCP gateway, deterministic router, policy layer, audit log, the disposable sandbox, and the scan lane. | Isolating tools and untrusted execution away from machines that hold anything valuable. |

## Design rules

- **Deterministic routing.** Which model or tool handles a task is decided by
  code and policy, not by a model. A model may *suggest* a route; the policy
  layer approves it.
- **Typed tool boundary.** Every tool has a strict schema and validates its
  inputs. Tools never build shell strings from input — they pass argument lists
  to subprocesses with the shell disabled.
- **Read vs. act separation.** Tools that read untrusted content are read-only.
  Anything that changes state requires explicit confirmation. No single tool
  both ingests untrusted content and holds the power to act on it.
- **Untrusted output is tagged.** Tool results that include external/untrusted
  content are marked as such so a planner treats them as data, not instructions.
- **Local-first exposure.** The tool gateway defaults to no network exposure;
  cross-machine access rides the private mesh only.
- **One model at a time.** On memory-constrained inference hardware, models are
  swapped, not co-resident, and the swap cost is accounted for in routing.

## Sandbox boundary

Untrusted / model-generated code runs in a container that is:

- network-isolated (no outbound access by default),
- capability-dropped and non-root,
- read-only root filesystem with a small writable scratch space,
- memory / CPU / process-count limited,
- auto-removed on completion or timeout.

The boundary is validated with explicit escape tests (network, filesystem
write, privilege, resource exhaustion, timeout) — containment is demonstrated,
not asserted.
