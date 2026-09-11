# Providers: OpenAI + Anthropic through Microsoft.Extensions.AI

Not a comparison. The point is: **business logic never knows which model it's talking to.**

## The abstraction chain

```
OpenAIClient ─┐
              ├─► IChatClient (Microsoft.Extensions.AI) ─► AIAgent (MAF) ─► your code
AnthropicClient ┘
```

- `IChatClient` — provider-neutral chat interface. Middleware (function invocation, OpenTelemetry, caching) composes on it via `ChatClientBuilder`.
- `AIAgent` — MAF's agent abstraction. Built from any `IChatClient` (`ChatClientAgent`) or directly from provider SDKs via `AsAIAgent(...)` extensions.
- `HarnessAgent` (v0.4) — built from an `IChatClient` via `AsHarnessAgent()`.

Your `Agents` package exposes factories that return `AIAgent`. Which provider backs it is a config choice in `Infrastructure`.

## Packages

| Provider | NuGet | Client | Notes |
|---|---|---|---|
| OpenAI | `Microsoft.Agents.AI.OpenAI` (+ `OpenAI`) | `OpenAIClient` → `GetResponsesClient()` (recommended) or `GetChatClient(model)` | Responses API = full hosted tools (code interpreter, file search, web search, hosted MCP) |
| Anthropic | `Microsoft.Agents.AI.Anthropic` (+ `Anthropic`) | `AnthropicClient` | Function tools ✅, tool approval ✅, local + hosted MCP ✅. No code interpreter / file search / web search in .NET client today |
| Anthropic on Foundry | + `Anthropic.Foundry`, `Azure.Identity` | `AnthropicFoundryClient` | v0.7 |
| Azure OpenAI / Foundry | `Microsoft.Agents.AI.OpenAI` + `Azure.AI.OpenAI`, or `Microsoft.Agents.AI.Foundry` + `Azure.AI.Projects` | `AzureOpenAIClient` / `AIProjectClient` | v0.7 |

`Microsoft.Agents.AI` core is GA (1.0, Apr 2026). Provider/integration packages may still be `--prerelease` — check NuGet when installing; drop the flag when a stable version exists.

## Minimal factories

```csharp
// OpenAI
AIAgent openAiAgent = new OpenAIClient(openAiKey)
    .GetChatClient(openAiModel)
    .AsAIAgent(instructions: instructions, name: "Architect");

// Anthropic
AIAgent anthropicAgent = new AnthropicClient { ApiKey = anthropicKey }
    .AsAIAgent(model: anthropicModel, name: "Architect", instructions: instructions);
```

Both return `AIAgent`. Same `RunAsync`, `RunStreamingAsync`, `CreateSessionAsync`.

## Config convention

```
Providers:Default = openai | anthropic
OPENAI_API_KEY, OPENAI_CHAT_MODEL_NAME
ANTHROPIC_API_KEY, ANTHROPIC_CHAT_MODEL_NAME
```

Use `dotnet user-secrets` locally. Never commit keys.

## Capability differences you'll hit

| Feature | OpenAI (Responses) | Anthropic |
|---|---|---|
| Function tools | ✅ | ✅ |
| Tool approval | ✅ | ✅ |
| Service-managed conversation history | ✅ | ❌ (in-memory / custom `ChatHistoryProvider`) |
| Hosted MCP | ✅ | ✅ |
| Code interpreter / file search | ✅ | ❌ |
| Background responses | ✅ | ❌ |
| Extended thinking | — | ✅ via `RawRepresentationFactory` |

When a phase touches one of these, note it in the phase's "Provider notes" and in the ADR.

## Model choice

Pick current models from each provider's catalog when you start v0.1; put them in config, not code. Prefer a small/fast model for dev loops and a larger one for the review/investigation agents.

## Sources

- [Model providers overview](https://learn.microsoft.com/agent-framework/integrations/by-component/model-providers/)
- [OpenAI provider](https://learn.microsoft.com/agent-framework/integrations/by-component/model-providers/openai)
- [Anthropic provider](https://learn.microsoft.com/agent-framework/integrations/by-component/model-providers/anthropic)
- [Microsoft.Extensions.AI libraries](https://learn.microsoft.com/dotnet/ai/microsoft-extensions-ai)
