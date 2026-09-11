# v0.5 — Incident Intelligence

**Vehicle:** second workspace. `apps/IncidentLab` emits fake logs/metrics for scenarios (DB timeout, cache outage, memory leak, cascading failure). The investigator agent builds a timeline, root-cause hypothesis and remediation.

**The real lesson:** reuse the v0.4 engine. Only tools + instructions + context change. If you find yourself copying agent code, the abstraction is wrong.

## MAF concepts (the goal)

- Engine reuse: same `HarnessAgent` factory, different tool set + instructions
- Context providers (`AIContextProvider`): inject runbook / scenario context before each turn
- Middleware: run middleware + function-invocation middleware (logging, arg injection, guardrails)
- Runtime context: `AgentSession.StateBag`, `AgentRunOptions.AdditionalProperties`
- Chat history provider — custom history when the provider has no service-managed history (Anthropic)

## Done when

- [ ] `apps/IncidentLab`: generates JSON logs + metrics for 4 scenarios into a folder or in-memory endpoint
- [ ] `Tools`: `QueryLogs`, `GetMetric`, `ListServices`, `ReadRunbook` — no code shared with repo tools except the base pattern
- [ ] `Agents`: `IncidentAgent.Create(...)` reuses the harness factory from v0.4 (one shared method, two callers)
- [ ] A context provider injects the active scenario's runbook automatically
- [ ] Function-invocation middleware logs every tool call; run middleware stamps a correlation id in `StateBag`
- [ ] Each scenario produces a timeline + hypothesis; both providers
- [ ] `apps/Api` exposes both workspaces on separate routes, same engine

## Out of scope

- Real observability backends (App Insights, Loki)
- Traces as input
- Alerting / remediation execution

## Build

1. `IncidentLab`: scenario generators (deterministic, seedable).
2. `Tools`: incident tools over the lab's output.
3. Refactor v0.4 factory → `EngineFactory.CreateHarness(chatClient, tools, instructions, providers)`.
4. `RunbookContextProvider : AIContextProvider`.
5. Middleware via `agent.AsBuilder().Use(...)` (run) and function-invocation middleware.

## Git

- Branch `feature/v0.5-incident-intelligence`
- Release `v0.5.0`
- **ADR-005: One engine, many workspaces** — what varies (tools, instructions, context), what doesn't (agent, harness, hosting).

## Resources

- [Context providers](https://learn.microsoft.com/agent-framework/concepts/agents/conversations/context-providers)
- [Step 4 — Memory & persistence](https://learn.microsoft.com/agent-framework/get-started/memory)
- [Middleware](https://learn.microsoft.com/agent-framework/concepts/agents/middleware/)
- [Runtime context](https://learn.microsoft.com/agent-framework/concepts/agents/middleware/runtime-context)
- [Chat history memory provider](https://learn.microsoft.com/agent-framework/concepts/agents/conversations/chat-history-memory-provider)
- [Agent pipeline architecture](https://learn.microsoft.com/agent-framework/concepts/agents/agent-pipeline)

## Stretch

- Structured output for the timeline (`record TimelineEntry(...)`).

Lab: not written yet — write `lab.md` against current MS Learn docs before starting this phase.
