# v0.7 — Foundry Edition

**Vehicle:** Arkandia Architect on Microsoft Foundry, two ways. Real subscription. See [foundry-guide.md](foundry-guide.md) for vocabulary and the two tracks.

## MAF / Foundry concepts (the goal)

Track A — build in Foundry:
- Prompt Agent (portal), versions, toolbox with MCP + function tools
- `FoundryAgent` from .NET (`Microsoft.Agents.AI.Foundry`, `AIProjectClient.AsAIAgent(agentRecord)`)
- Server-side tracing; cloud evaluations

Track B — host MAF in Foundry:
- `Microsoft.Agents.AI.Foundry.Hosting`: `AgentHost.CreateBuilder`, `AddFoundryResponses`, `MapFoundryResponses`
- `azd ai agent init/run/invoke/deploy`; container (linux/amd64) or source-code deploy
- Managed sessions/identity/scale; playground

Both:
- Anthropic on Foundry (`AnthropicFoundryClient` + `DefaultAzureCredential`)
- Foundry model inference as just another `IChatClient` (`Microsoft.Agents.AI.Foundry` model provider)

Note: Foundry Hosted Agents use **consumption-based billing** — set a budget alert on the resource group before deploying; tear down after each session.

## Done when

- [ ] Track A: Prompt Agent "Architecture Chat" runs in the portal with the Learn MCP toolbox; .NET client calls it via `FoundryAgent`; traces visible in portal; one cloud eval run with a report link
- [ ] Track B: `apps/Api` (or a thin `apps/FoundryHost`) exposes Responses protocol; runs locally on 8088; deployed with `azd deploy`; invoked from playground and from `azd ai agent invoke`
- [ ] Provider switch to Anthropic-on-Foundry works with **no** agent code change
- [ ] Written comparison (in ADR-007): what each track manages, what each costs you in control
- [ ] Resource cleanup script / `azd down` documented

## Out of scope

- Publishing to Teams / M365 Copilot
- Invocations / WebSocket protocol
- Agent optimizer
- VNet / private networking

## Build

1. Provision: Foundry project, OpenAI deployment, Anthropic deployment, App Insights (via `azd provision` or portal).
2. Track A in portal + `FoundryAgent` client sample in `apps/Cli`.
3. Track B: hosting project; local run; `azd` deploy; Docker `--platform linux/amd64`.
4. Evals: `FoundryEvals` from code on 5 architecture questions.
5. Cost check + teardown.

## Git

- Branch `feature/v0.7-foundry`
- Release `v0.7.0`
- **ADR-007: Where Foundry fits** — when to build in Foundry, when to host MAF in Foundry, when to self-host.

## Resources

- [Agents in Microsoft Foundry](https://learn.microsoft.com/azure/foundry/agents/overview)
- [Foundry Agent Service from MAF (Prompt/Hosted)](https://learn.microsoft.com/agent-framework/integrations/by-component/agent-services/foundry)
- [Foundry model provider](https://learn.microsoft.com/agent-framework/integrations/by-component/model-providers/microsoft-foundry)
- [Host MAF agents as Foundry hosted agents](https://learn.microsoft.com/azure/foundry/how-to/develop/framework-hosted-agents)
- [Foundry Hosted Agents (MAF)](https://learn.microsoft.com/agent-framework/hosting/foundry-hosted-agent)
- [Quickstart: deploy first hosted agent (C#)](https://learn.microsoft.com/azure/foundry/agents/quickstarts/quickstart-hosted-agent)
- [Deploy hosted agent from source](https://learn.microsoft.com/azure/foundry/agents/how-to/deploy-hosted-agent-code)
- [Deploy a hosted agent (azd)](https://learn.microsoft.com/azure/foundry/agents/how-to/deploy-hosted-agent)
- [Connect agents to MCP servers (Foundry)](https://learn.microsoft.com/azure/foundry/agents/how-to/tools/model-context-protocol)
- [Set up tracing](https://learn.microsoft.com/azure/foundry/observability/how-to/trace-agent-setup)
- [Foundry evaluation (MAF)](https://learn.microsoft.com/agent-framework/integrations/by-component/evaluation/microsoft-foundry)
- [Anthropic on Foundry](https://learn.microsoft.com/agent-framework/integrations/by-component/model-providers/anthropic#using-anthropic-on-foundry)
- [Hosting options overview](https://learn.microsoft.com/agent-framework/hosting/)

Lab: not written yet — write `lab.md` against current MS Learn docs before starting this phase.
