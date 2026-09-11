# v0.3 — Architecture Review Board

**Vehicle:** several specialist agents (Architecture, Security, Quality, Performance) review a repo in parallel; a Lead agent merges findings into one report.

## MAF concepts (the goal)

- Workflows: `AgentWorkflowBuilder.BuildConcurrent`, `BuildSequential`
- Workflow events: `AgentResponseUpdateEvent`, workflow output
- Workflow state: `QueueStateUpdateAsync` / `ReadStateAsync` with shared scope; superstep visibility
- State isolation: build a fresh workflow per request; `IResettableExecutor` if sharing executors
- Custom executor (the Lead aggregator) vs agent-as-executor

## Done when

- [ ] 4 specialist agents share the v0.2 tools, differ only in instructions
- [ ] Concurrent workflow runs them in parallel; streaming shows interleaved progress
- [ ] Lead executor reads all results (shared state or aggregated messages) → single markdown report
- [ ] Sequential variant exists (specialists → lead) and you can explain when you'd pick each
- [ ] Workflow is exposed as an `AIAgent` (`AddWorkflow(...).AddAsAIAgent()`) and callable from `apps/Api`
- [ ] Works with both providers; mixed board (some agents OpenAI, some Anthropic) works too

## Out of scope

- Handoff / group chat / Magentic orchestrations — all GA now; read about them, don't build. Magentic is the natural v0.3 stretch if the board needs a dynamic planner.
- Checkpointing / durable workflows
- Report rendering beyond markdown

## Build

1. `Agents`: `ReviewerAgents.Architecture/Security/Quality/Performance(provider)`.
2. `Workflows`: `ReviewBoardWorkflow.Build(...)` — helper method returns fresh instances per call.
3. Lead as custom executor: collects, dedupes, ranks findings.
4. `Api`: `POST /review` → runs workflow, streams events as SSE.
5. Run on your own repo; save the report to `docs/assets/review-v0.3.md`.

## Provider notes

- Mixed-provider board is the real test of the abstraction.

## Git

- Branch `feature/v0.3-review-board`
- Release `v0.3.0`
- **ADR-003: Workflows instead of custom orchestration** — what you'd have had to write yourself (fan-out, aggregation, state visibility, streaming).

## Resources

- [Step 5 — Workflows](https://learn.microsoft.com/agent-framework/get-started/workflows)
- [Workflows overview](https://learn.microsoft.com/agent-framework/concepts/workflows/)
- [Concurrent orchestration](https://learn.microsoft.com/agent-framework/workflows/orchestrations/concurrent)
- [Sequential orchestration](https://learn.microsoft.com/agent-framework/workflows/orchestrations/sequential)
- [Workflow state](https://learn.microsoft.com/agent-framework/concepts/workflows/state)
- [Agents in workflows](https://learn.microsoft.com/agent-framework/workflows/agents-in-workflows)
- [.NET samples: 03-workflows](https://github.com/microsoft/agent-framework/tree/main/dotnet/samples/03-workflows)

## Stretch

- Add a `RequestInfoEvent` pause: Lead asks the human "which finding to expand?" before finishing.

Lab: not written yet — write `lab.md` against current MS Learn docs before starting this phase.
