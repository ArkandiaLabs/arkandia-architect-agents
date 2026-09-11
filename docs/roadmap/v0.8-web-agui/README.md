# v0.8 — Web + AG-UI

**Vehicle:** a Next.js UI showing active agents, tool calls, approvals, and the investigation timeline. Backend speaks AG-UI.

Division of labor: **you** learn the AG-UI protocol and MAF hosting side; the React/Next.js code can be generated (Claude Code) — it's not the learning objective.

## MAF concepts (the goal)

- `Microsoft.Agents.AI.Hosting.AGUI.AspNetCore`: `AddAGUIServer`, `MapAGUIServer("/", agent)`
- AG-UI event model over SSE: text, tool calls, approvals, state snapshots/deltas
- Human-in-the-loop: MAF tool approval → AG-UI approval event → client decision → resume
- State management: `AGUIStreamOptions` mapping tool results → shared state
- Conversation continuity via AG-UI `threadId` ↔ persisted session
- Workflows exposed through AG-UI (`AddAsAIAgent`)

## Done when

- [ ] `apps/Api` maps Review Board workflow-agent and Incident agent on AG-UI endpoints
- [ ] Next.js client (CopilotKit `HttpAgent` or raw SSE) streams text + tool events
- [ ] Approval prompt appears in UI, decision resumes the run
- [ ] Investigation timeline rendered from AG-UI state events (opt-in mapping)
- [ ] Refresh the page → thread resumes via `threadId`
- [ ] Verified against AG-UI Dojo (run the .NET integration locally)

## Out of scope

- Auth, multi-tenant
- Design polish
- Generative UI beyond one component

## Build

1. AG-UI endpoints in `Api`; test with the AG-UI .NET console client first.
2. Run AG-UI Dojo against your endpoint to validate protocol conformance.
3. Generate Next.js app in `apps/Web`; wire CopilotKit or a minimal SSE client.
4. Map tool results → state with `AGUIStreamOptions`.

## Git

- Branch `feature/v0.8-web-agui`
- Release `v0.8.0`
- **ADR-008: AG-UI instead of a custom SSE protocol** — event types you didn't have to invent; what the client gets for free.

## Resources

- [AG-UI integration overview](https://learn.microsoft.com/agent-framework/integrations/by-component/ui/ag-ui/)
- [Getting started with AG-UI](https://learn.microsoft.com/agent-framework/integrations/by-component/ui/ag-ui/getting-started)
- [Backend tool rendering](https://learn.microsoft.com/agent-framework/integrations/by-component/ui/ag-ui/backend-tool-rendering)
- [Human in the loop](https://learn.microsoft.com/agent-framework/integrations/by-component/ui/ag-ui/human-in-the-loop)
- [State management](https://learn.microsoft.com/agent-framework/integrations/by-component/ui/ag-ui/state-management)
- [Workflows with AG-UI](https://learn.microsoft.com/agent-framework/integrations/by-component/ui/ag-ui/workflows)
- [Testing with AG-UI Dojo](https://learn.microsoft.com/agent-framework/integrations/by-component/ui/ag-ui/testing-with-dojo)
- [CopilotKit × MAF](https://docs.copilotkit.ai/microsoft-agent-framework)

Lab: not written yet — write `lab.md` against current MS Learn docs before starting this phase.
