# Microsoft Foundry — two tracks

Real Azure subscription. Phase [v0.7](README.md). Goal: understand Foundry from both sides so you can advise when to use which.

## Vocabulary

| Term | Meaning |
|---|---|
| Foundry project | Azure resource; endpoint `https://<resource>.services.ai.azure.com/api/projects/<project>` |
| Model deployment | A model made available in the project (OpenAI, Anthropic, others from catalog) |
| **Prompt Agent** | Declarative, server-side agent definition (model + instructions + tools), versioned, run by Foundry |
| **Hosted Agent** | *Your code* (e.g. a MAF app) packaged as a container or source zip, run by Foundry with managed sessions, identity, scale |
| Toolbox | Curated set of tools (MCP servers, functions, web search…) exposed as one managed MCP endpoint |
| Responses protocol | OpenAI-compatible `/responses` endpoint a hosted agent exposes; Foundry manages history + streaming |
| Invocations protocol | Generic `/invocations` endpoint for non-conversational work |

## Track A — Build directly in Foundry

You learn what Foundry gives you *without* writing an agent loop.

- Create a Prompt Agent in the portal for Architecture Chat (same instructions as v0.1).
- Attach a toolbox: Microsoft Learn MCP + a function tool.
- Connect from .NET with `Microsoft.Agents.AI.Foundry` (`FoundryAgent` via `AIProjectClient.AsAIAgent(agentRecord)`).
- Enable tracing; inspect server-side traces in the portal.
- Run a cloud evaluation (relevance, coherence, task adherence).

Outcome: you can explain what Foundry manages for you (definition, versioning, history, tracing, evals, identity) and where you lose control (can't change model/tools at run time from the client).

## Track B — Host your MAF app in Foundry

You learn how *your* code runs inside Foundry.

- Take `apps/Api` agent. Add `Microsoft.Agents.AI.Foundry.Hosting`.
- Expose via Responses protocol: `AgentHost.CreateBuilder` + `AddFoundryResponses` + `MapFoundryResponses`.
- Run locally (`azd ai agent run`, port 8088), invoke with `azd ai agent invoke --local`.
- Deploy with `azd deploy` (Docker, linux/amd64). Or source-code deploy (`.NET SDK zips a folder, Foundry runs dotnet publish`).
- Same code now has managed sessions, agent identity, tracing, playground.

Outcome: you can explain the difference between *self-hosting* and *Foundry-hosting* the same `AIAgent`, and what changed (nothing in agent logic; everything in hosting).

## Also in v0.7

- Anthropic on Foundry: `AnthropicFoundryClient` with `DefaultAzureCredential` — proves the provider abstraction survives a hosting change.
- Foundry evals from code: `FoundryEvals` in `Microsoft.Agents.AI` evaluation APIs.

## Prereqs to set up before v0.7

- Azure subscription; Foundry project; one OpenAI deployment; Anthropic model deployment (if available in your region).
- Roles: **Foundry Project Manager** on the project (needed for hosted agent deploy).
- `az login`, `azd`, `azd ext install azure.ai.agents`, Docker.
- .NET 10 SDK.

## Sources

- [Agents in Microsoft Foundry (overview)](https://learn.microsoft.com/azure/foundry/agents/overview)
- [Foundry Agent Service integration (Prompt/Hosted agents from MAF)](https://learn.microsoft.com/agent-framework/integrations/by-component/agent-services/foundry)
- [Host MAF agents as Foundry hosted agents](https://learn.microsoft.com/azure/foundry/how-to/develop/framework-hosted-agents)
- [Foundry Hosted Agents (MAF hosting docs)](https://learn.microsoft.com/agent-framework/hosting/foundry-hosted-agent)
- [Quickstart: deploy your first hosted agent (C#)](https://learn.microsoft.com/azure/foundry/agents/quickstarts/quickstart-hosted-agent)
- [Deploy a hosted agent from source code](https://learn.microsoft.com/azure/foundry/agents/how-to/deploy-hosted-agent-code)
- [Set up tracing in Foundry](https://learn.microsoft.com/azure/foundry/observability/how-to/trace-agent-setup)
- [Foundry evaluation from MAF](https://learn.microsoft.com/agent-framework/integrations/by-component/evaluation/microsoft-foundry)
- [Anthropic on Foundry](https://learn.microsoft.com/agent-framework/integrations/by-component/model-providers/anthropic#using-anthropic-on-foundry)
