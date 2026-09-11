# v0.4 — Autonomous Architecture Investigation

**Vehicle:** "Evaluate whether this solution follows Clean Architecture." The agent plans, investigates with tools, delegates sub-tasks, and produces a report + draft ADR — without step-by-step prompting.

Note: background agents, looping, file access and shell tools are **opt-in and emit warnings when enabled** — expect API movement there; core harness (planning, todos, modes, compaction, approval, OTel) is stable.

## MAF concepts (the goal)

- `HarnessAgent` (`Microsoft.Agents.AI.Harness`): `chatClient.AsHarnessAgent(HarnessAgentOptions)`
- Planning & todos (`TodoProvider`), plan/execute modes (`AgentModeProvider`)
- Context compaction (`MaxContextWindowTokens`), file memory, file access (`FileAccessStore`)
- Background agents (delegate to named child agents)
- Looping (`LoopEvaluators`, `LoopAgentOptions.MaxIterations`) — bounded autonomy
- Tool approval with standing approvals
- OpenTelemetry: harness instruments chat-client + agent spans by default; view in Aspire dashboard or console exporter

## Done when

- [ ] `HarnessAgent` built from the v0.1 `IChatClient` with v0.2 tools
- [ ] Console shows todos + current mode while it works (use the sample terminal UX as reference, not as dependency)
- [ ] One background agent (e.g. "dependency-mapper") delegated to
- [ ] A loop evaluator ends the run on a completion marker, max N iterations
- [ ] Traces exported (OTLP → Aspire dashboard, or console) show plan/tool/llm spans
- [ ] Output: findings report + draft ADR in markdown
- [ ] Runs with both providers (Anthropic via its `IChatClient` — verify `AsIChatClient` availability when writing the lab)

## Out of scope

- Shell tools (harness supports them; skip unless trivial)
- Agent Skills
- Persisting harness state beyond one session
- Perfect Clean-Arch detection heuristics

## Build

1. `Agents`: `InvestigatorAgent.Create(chatClient, tools)` → `HarnessAgent` with `HarnessInstructions`.
2. `Cli`: loop with `AgentSession`; render todo/mode from response contents; approval prompt.
3. Add `BackgroundAgents` with one child.
4. Add `LoopEvaluators = [new CompletionMarkerLoopEvaluator("DONE")]`, `MaxIterations = 5`.
5. Add `Sdk.CreateTracerProviderBuilder().AddSource(...).AddOtlpExporter()`; run Aspire dashboard container.

## Git

- Branch `feature/v0.4-autonomous-investigation`
- Release `v0.4.0`
- **ADR-004: Harness for autonomous execution** — what the harness composes for you, where its opinions constrain you, and why bounded loops.

## Resources

- [Agent Harness (concepts)](https://learn.microsoft.com/agent-framework/concepts/harness)
- [Step 6 — Agent Harness](https://learn.microsoft.com/agent-framework/get-started/harness)
- [Planning and todos](https://learn.microsoft.com/agent-framework/agents/planning-and-todos)
- [Background agents](https://learn.microsoft.com/agent-framework/agents/background-agents)
- [Agent looping](https://learn.microsoft.com/agent-framework/agents/looping)
- [Compaction](https://learn.microsoft.com/agent-framework/concepts/agents/conversations/compaction)
- [Observability](https://learn.microsoft.com/agent-framework/agents/observability)
- [.NET Harness samples](https://github.com/microsoft/agent-framework/tree/main/dotnet/samples/02-agents/Harness)

## Stretch

- Compare a hand-rolled "plan → act → reflect" loop (v0.3 style) with the harness; put the diff in the ADR.

Lab: not written yet — write `lab.md` against current MS Learn docs before starting this phase.
