# v0.1 — Architecture Chat

**Vehicle:** a console chat where you paste/point to an architecture doc (README, ADR, design md) and ask questions about it.

## MAF concepts (the goal)

- `AIAgent` — create, `RunAsync`, `RunStreamingAsync`
- `IChatClient` from `Microsoft.Extensions.AI` as the provider seam
- `AgentSession` — multi-turn state, `SerializeSession` / `DeserializeSessionAsync`
- Provider abstraction: same agent code, OpenAI or Anthropic by config

## Done when

- [ ] `apps/Cli` runs a multi-turn chat; agent remembers prior turns (`AgentSession`)
- [ ] Responses stream to the console
- [ ] Switching `Providers:Default` between `openai` and `anthropic` requires **zero** code change
- [ ] A markdown file path is injected as context (simple: read file → prepend to instructions or first message)
- [ ] Session can be saved to disk and resumed
- [ ] Solution builds with the monorepo layout: `apps/Cli`, `packages/Core`, `packages/Agents`, `packages/Infrastructure`

## Out of scope

- Tools, RAG, embeddings, chunking
- Web API, UI
- Prompt engineering polish
- Handling docs bigger than the context window

## Build

1. Solution + projects per [architecture.md](../../architecture/architecture.md) (only the four above).
2. `Infrastructure`: `IChatClient`/agent factory for both providers, config via user-secrets.
3. `Agents`: `ArchitectAgentFactory.Create(provider)` → `AIAgent` with architect instructions.
4. `Cli`: loop — read input, `RunStreamingAsync(input, session)`, print updates; `/save`, `/load`, `/exit`.
5. Run the same 3 questions against both providers; paste outputs in the PR.

## Provider notes

- Anthropic: no service-managed history → session history is in-memory/custom. OpenAI Responses can keep history server-side. Both work with `AgentSession`; note the difference in ADR-001.

## Git

- Branch `feature/v0.1-architecture-chat`
- PR title: `v0.1: Architecture Chat — AIAgent, sessions, two providers`
- Release `v0.1.0`
- **ADR-001: Why Microsoft Agent Framework** (vs LangChain/Deep Agents, vs raw `Microsoft.Extensions.AI`). Draft with context already exists at [ADR-001](../../architecture/adr/ADR-001-why-microsoft-agent-framework.md) — complete Decision, Consequences, What I learned, Provider notes.

## Resources

- [Get started: Step 1 — Your first agent](https://learn.microsoft.com/agent-framework/get-started/your-first-agent)
- [Step 3 — Multi-turn conversations](https://learn.microsoft.com/agent-framework/get-started/multi-turn)
- [Conversations & memory overview](https://learn.microsoft.com/agent-framework/concepts/agents/conversations/)
- [Session](https://learn.microsoft.com/agent-framework/concepts/agents/conversations/session)
- [OpenAI provider](https://learn.microsoft.com/agent-framework/integrations/by-component/model-providers/openai)
- [Anthropic provider](https://learn.microsoft.com/agent-framework/integrations/by-component/model-providers/anthropic)
- [Microsoft.Extensions.AI](https://learn.microsoft.com/dotnet/ai/microsoft-extensions-ai)
- [.NET samples: 01-get-started](https://github.com/microsoft/agent-framework/tree/main/dotnet/samples/01-get-started)

## Stretch

- Add `TextReasoningContent` display for Anthropic extended thinking.
- Add a `ChatClientBuilder` with `UseOpenTelemetry()` and print spans to console — preview of v0.4.

Lab: [lab.md](lab.md)
