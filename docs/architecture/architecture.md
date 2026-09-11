# Architecture

## Product

**Arkandia Architect** — an AI platform that understands software systems.

Two *workspaces* (product modules) on one *agent engine*:

| Workspace | Behaves like | Input | Output | Appears in |
|---|---|---|---|---|
| Architecture Review | Senior architect | Repo, ADRs, diagrams, README | Review, Clean-Arch validation, findings, generated ADR | v0.1+ |
| Incident Intelligence | Incident commander | Logs, metrics, runbooks | Timeline, root-cause hypothesis, evidence, remediation | v0.5+ |

Future (not planned): ADR Studio, System Explorer.

"Workspace" is a product term, not a MAF term. Think Cursor: Chat / Composer / Background Agents — different entry points, same engine.

```
             Arkandia Architect
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
 Architecture   Incident     (future)
   Review     Intelligence
       │            │
       └────────────┼────────────┘
                    ▼
            MAF Agent Engine
   agents · tools · workflows · harness · mcp
```

## Stack

| Layer | Choice |
|---|---|
| Language | C# / .NET 10 |
| AI abstraction | `Microsoft.Extensions.AI` (`IChatClient`, `AIFunction`) |
| Agents | Microsoft Agent Framework (`Microsoft.Agents.AI.*`) |
| Models | OpenAI + Anthropic (see architecture/providers.md) |
| Observability | OpenTelemetry (MAF emits GenAI semconv spans) |
| Protocol to UI | REST + SSE first, AG-UI in v0.8 |
| MCP | MCP C# SDK via MAF |
| Hosting | ASP.NET Core; Foundry Hosted Agent in v0.7; Docker/Aspire in v1.0 |
| UI | Next.js (v0.8) |

## Repo layout (monorepo + Clean Architecture)

PascalCase for anything that maps to a .NET project.

```
arkandia-architect-agents/
├── Directory.Build.props      # TargetFramework, LangVersion, Nullable, analyzers, RootNamespace
├── Directory.Packages.props   # Central Package Management — every PackageVersion in one place
├── global.json                # pins .NET SDK version
├── apps/
│   ├── Api/                 # ASP.NET Core host — agents, workflows, endpoints
│   ├── Cli/                 # console runner (v0.1–v0.7 dev loop)
│   ├── Web/                 # Next.js (v0.8)
│   └── IncidentLab/         # fake telemetry generator (v0.5)
├── packages/
│   ├── Core/
│   │   ├── Domain/          # entities, value objects, no deps
│   │   ├── Application/     # use cases, ports (interfaces)
│   │   └── Contracts/       # DTOs shared with apps
│   ├── Agents/              # AIAgent factories, instructions, HarnessAgent config
│   ├── Workflows/           # MAF workflow builders
│   ├── Tools/               # AIFunction implementations
│   ├── Mcp/                 # MCP clients/servers
│   └── Infrastructure/      # provider clients, storage, telemetry
├── tests/
├── docs/
│   ├── README.md
│   ├── architecture/        # architecture.md, providers.md, adr/
│   ├── roadmap/             # README (table) + vX.Y-*/ {README.md, lab.md}
│   ├── study-plan.md
│   ├── assets/
│   └── templates/
├── docker/
└── .github/
    ├── workflows/
    ├── ISSUE_TEMPLATE/
    └── PULL_REQUEST_TEMPLATE.md
```

Project names: `Arkandia.Architect.<Folder>` (e.g. `Arkandia.Architect.Agents.csproj`).

## Build & version management

Three root files, no per-project versions:

| File | Owns |
|---|---|
| `global.json` | .NET SDK version (`rollForward: latestFeature`) |
| `Directory.Build.props` | `TargetFramework=net10.0`, `Nullable`, `ImplicitUsings`, `TreatWarningsAsErrors`, `RootNamespace`, analyzers |
| `Directory.Packages.props` | `ManagePackageVersionsCentrally=true` + one `<PackageVersion>` per NuGet. Projects use `<PackageReference Include="X" />` with **no** `Version` |

Rule: bumping MAF = editing one line in `Directory.Packages.props`. That's how you survive prerelease churn across 8 projects.

Not every folder exists on day one — v0.1 creates `apps/Cli`, `packages/Core`, `packages/Agents`, `packages/Infrastructure`. The rest appear when their phase needs them.

## Dependency rule

```
apps/Api, apps/Cli
        │
        ▼
    Workflows
        │
        ▼
      Agents ──► Tools ──► Mcp
        │
        ▼
   Application
        │
        ▼
     Domain
        ▲
        │
  Infrastructure   (implements Application ports)
```

Nothing points inward except abstractions. `Domain` has zero package refs. `Application` references only `Microsoft.Extensions.AI.Abstractions` if it must expose `IChatClient`-shaped ports — prefer your own port interfaces.

## Where MAF concepts live

| MAF concept | Package folder |
|---|---|
| `IChatClient` construction, provider config | `Infrastructure` |
| `AIAgent` / `HarnessAgent` factories, instructions | `Agents` |
| `AIFunction` tools | `Tools` |
| Workflow builders, executors | `Workflows` |
| `AgentSession` storage | `Infrastructure` (port in `Application`) |
| MCP client/server | `Mcp` |
| Hosting (`AddAIAgent`, `MapAGUIServer`) | `apps/Api` |
