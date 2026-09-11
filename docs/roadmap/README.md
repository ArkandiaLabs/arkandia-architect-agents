# Roadmap

Hierarchy: this table → each phase folder (`README.md` = guide) → its `lab.md` (written just before starting the phase). Method for working a phase: [../study-plan.md](../study-plan.md).

Each phase = feature (vehicle) + MAF concepts (goal) + PR + GitHub Release + ADR.


## Phases

| Phase | Vehicle | MAF concepts (the goal) | Azure | ADR | Lab |
|---|---|---|---|---|---|
| [v0.1](v0.1-architecture-chat/README.md) | Architecture Chat | `AIAgent`, `IChatClient`, streaming, `AgentSession`, provider abstraction (OpenAI + Anthropic) | — | 001 Why MAF | [lab](v0.1-architecture-chat/lab.md) |
| [v0.2](v0.2-repository-intelligence/README.md) | Repository Intelligence | Function tools (`AIFunctionFactory`), tool approval, DI, hosting in ASP.NET Core | — | 002 Monorepo layout | — |
| [v0.3](v0.3-review-board/README.md) | Architecture Review Board | Workflows: concurrent + sequential orchestration, shared state, events | — | 003 Workflows vs custom orchestration | — |
| [v0.4](v0.4-autonomous-investigation/README.md) | Autonomous Investigation | `HarnessAgent`: planning/todos, modes, compaction, file memory, background agents, looping, OTel | — | 004 Harness for autonomy | — |
| [v0.5](v0.5-incident-intelligence/README.md) | Incident Intelligence | Engine reuse; context providers, middleware, session `StateBag` | — | 005 One engine, many workspaces | — |
| [v0.6](v0.6-mcp/README.md) | MCP Ecosystem | MCP client (local/stdio), hosted MCP tools, expose agent as MCP server | — | 006 MCP vs direct integrations | — |
| [v0.7](v0.7-foundry/README.md) | Foundry Edition | Track A: Prompt Agents, toolboxes, tracing, evals. Track B: MAF hosted agent (Responses protocol), `azd` | **Real** | 007 Where Foundry fits | — |
| [v0.8](v0.8-web-agui/README.md) | Web + AG-UI | `MapAGUIServer`, AG-UI events, human-in-the-loop in UI, state sync; Next.js client | — | 008 AG-UI vs custom SSE | — |
| [v1.0](v1.0-hardening/README.md) | Hardening | Session persistence, evaluation (`LocalEvaluator` + Foundry evals), OTel end-to-end, Aspire, Docker | optional | 009 Production posture | — |

## Dependency map

```
v0.1 Chat ──► v0.2 Tools ──► v0.3 Workflows ──► v0.4 Harness ──► v0.5 Incident
                                                                     │
                                                                     ▼
                                                                  v0.6 MCP
                                                                     │
                                                                     ▼
                                                                v0.7 Foundry
                                                                     │
                                                                     ▼
                                                                v0.8 AG-UI ──► v1.0 Hardening
```

Core MAF learning is v0.1–v0.6. v0.7 is the Azure priority. v0.8/v1.0 are product-completion phases — do them, but they teach less MAF per hour.
