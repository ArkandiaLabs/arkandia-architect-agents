# Lab v0.1 — Architecture Chat

Hands-on for [v0.1 — Architecture Chat](README.md). Method: [study-plan.md](../../study-plan.md).
Core MAF is GA; provider packages may still need `--prerelease`. If an API below doesn't compile, check the linked doc first, then adapt.

## Prereqs

- .NET 10 SDK
- OpenAI API key, Anthropic API key
- GitHub repo `arkandia-architect-agents` created, empty, `main` branch

## Step 0 — Learn first (30 min)

Read, then run the official sample before writing your own:

1. [Step 1: Your first agent](https://learn.microsoft.com/agent-framework/get-started/your-first-agent)
2. [Step 3: Multi-turn conversations](https://learn.microsoft.com/agent-framework/get-started/multi-turn)
3. [OpenAI provider](https://learn.microsoft.com/agent-framework/integrations/by-component/model-providers/openai) — Chat Completion + Responses clients
4. [Anthropic provider](https://learn.microsoft.com/agent-framework/integrations/by-component/model-providers/anthropic) — basic agent creation
5. Clone `microsoft/agent-framework`, run `dotnet/samples/01-get-started/01_hello_agent`

Write in the issue: what `AsAIAgent` does; what `AgentSession` holds; what streams in `RunStreamingAsync`.

## Step 1 — Issue + branch

```bash
# create issue on GitHub (Phase template auto-loads from .github/) — title "v0.1: Architecture Chat"
git checkout -b feature/v0.1-architecture-chat
```

## Step 2 — Solution skeleton

```bash
mkdir -p apps/Cli packages/Core/Domain packages/Core/Application packages/Agents packages/Infrastructure tests

dotnet new globaljson --sdk-version $(dotnet --version) --roll-forward latestFeature
dotnet new sln -n ArkandiaArchitect
dotnet new classlib -n Arkandia.Architect.Domain        -o packages/Core/Domain
dotnet new classlib -n Arkandia.Architect.Application   -o packages/Core/Application
dotnet new classlib -n Arkandia.Architect.Agents        -o packages/Agents
dotnet new classlib -n Arkandia.Architect.Infrastructure -o packages/Infrastructure
dotnet new console  -n Arkandia.Architect.Cli           -o apps/Cli

dotnet sln add packages/Core/Domain packages/Core/Application packages/Agents packages/Infrastructure apps/Cli

# dependency rule
dotnet add packages/Core/Application reference packages/Core/Domain
dotnet add packages/Agents            reference packages/Core/Application
dotnet add packages/Infrastructure    reference packages/Core/Application
dotnet add apps/Cli                   reference packages/Agents packages/Infrastructure
```

### Root build files

`Directory.Build.props` — applies to every project:

```xml
<Project>
  <PropertyGroup>
    <TargetFramework>net10.0</TargetFramework>
    <LangVersion>latest</LangVersion>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
    <RootNamespace>$(MSBuildProjectName)</RootNamespace>
    <EnableNETAnalyzers>true</EnableNETAnalyzers>
    <AnalysisLevel>latest</AnalysisLevel>
  </PropertyGroup>
</Project>
```

`Directory.Packages.props` — Central Package Management, single source of versions.
Get current versions from NuGet (`dotnet package search Microsoft.Agents.AI --prerelease`) and pin them here:

```xml
<Project>
  <PropertyGroup>
    <ManagePackageVersionsCentrally>true</ManagePackageVersionsCentrally>
    <CentralPackageTransitivePinningEnabled>true</CentralPackageTransitivePinningEnabled>
  </PropertyGroup>
  <ItemGroup>
    <!-- MAF -->
    <PackageVersion Include="Microsoft.Agents.AI"           Version="<pin>" />
    <PackageVersion Include="Microsoft.Agents.AI.OpenAI"    Version="<pin>" />
    <PackageVersion Include="Microsoft.Agents.AI.Anthropic" Version="<pin>" />
    <!-- Microsoft.Extensions -->
    <PackageVersion Include="Microsoft.Extensions.Configuration.Abstractions" Version="<pin>" />
    <PackageVersion Include="Microsoft.Extensions.Configuration.UserSecrets"  Version="<pin>" />
    <PackageVersion Include="Microsoft.Extensions.Hosting"                    Version="<pin>" />
    <!-- Tests -->
    <PackageVersion Include="xunit"                    Version="<pin>" />
    <PackageVersion Include="xunit.runner.visualstudio" Version="<pin>" />
    <PackageVersion Include="Microsoft.NET.Test.Sdk"   Version="<pin>" />
  </ItemGroup>
</Project>
```

Then strip `<TargetFramework>`, `<Nullable>`, `<ImplicitUsings>` from each generated `.csproj` (the props own them) and add references **without versions**:

```bash
dotnet add packages/Agents         package Microsoft.Agents.AI
dotnet add packages/Infrastructure package Microsoft.Agents.AI.OpenAI
dotnet add packages/Infrastructure package Microsoft.Agents.AI.Anthropic
dotnet add packages/Infrastructure package Microsoft.Extensions.Configuration.Abstractions
dotnet add apps/Cli package Microsoft.Extensions.Configuration.UserSecrets
dotnet add apps/Cli package Microsoft.Extensions.Hosting
```

`dotnet add package` with CPM enabled writes the version into `Directory.Packages.props`, not the csproj. If you get NU1008, a csproj still has an inline `Version` — remove it.

Bumping MAF later = edit one line in `Directory.Packages.props`.

Secrets:

```bash
cd apps/Cli
dotnet user-secrets init
dotnet user-secrets set "Providers:Default" "openai"
dotnet user-secrets set "OpenAI:ApiKey" "<key>"
dotnet user-secrets set "OpenAI:Model" "<current small model>"
dotnet user-secrets set "Anthropic:ApiKey" "<key>"
dotnet user-secrets set "Anthropic:Model" "<current small model>"
cd ../..
```

## Step 3 — Application port

`packages/Core/Application/Agents/IArchitectAgentFactory.cs`

```csharp
using Microsoft.Agents.AI;

namespace Arkandia.Architect.Application.Agents;

public enum ModelProvider { OpenAI, Anthropic }

public interface IArchitectAgentFactory
{
    AIAgent Create(ModelProvider provider, string? documentContext = null);
}
```

> Application referencing `Microsoft.Agents.AI` for the `AIAgent` type is a pragmatic v0.1 choice. Note it in ADR-001; revisit if it leaks further.

Add to Application: `dotnet add packages/Core/Application package Microsoft.Agents.AI` (version already pinned centrally).

## Step 4 — Infrastructure: provider clients

`packages/Infrastructure/Providers/ProviderOptions.cs`

```csharp
namespace Arkandia.Architect.Infrastructure.Providers;

public sealed class ProviderOptions
{
    public string Default { get; set; } = "openai";
    public OpenAIOptions OpenAI { get; set; } = new();
    public AnthropicOptions Anthropic { get; set; } = new();
}
public sealed class OpenAIOptions { public string ApiKey { get; set; } = ""; public string Model { get; set; } = ""; }
public sealed class AnthropicOptions { public string ApiKey { get; set; } = ""; public string Model { get; set; } = ""; }
```

`packages/Infrastructure/Agents/ArchitectAgentFactory.cs`

```csharp
using Anthropic;
using Arkandia.Architect.Application.Agents;
using Arkandia.Architect.Infrastructure.Providers;
using Microsoft.Agents.AI;
using OpenAI;

namespace Arkandia.Architect.Infrastructure.Agents;

public sealed class ArchitectAgentFactory(ProviderOptions options) : IArchitectAgentFactory
{
    private const string BaseInstructions = """
        You are a senior software architect. Answer questions about the provided
        architecture documents. Be precise, cite the section you rely on, and say
        when the document does not contain the answer.
        """;

    public AIAgent Create(ModelProvider provider, string? documentContext = null)
    {
        var instructions = documentContext is null
            ? BaseInstructions
            : $"{BaseInstructions}\n\n<document>\n{documentContext}\n</document>";

        return provider switch
        {
            ModelProvider.OpenAI => new OpenAIClient(options.OpenAI.ApiKey)
                .GetChatClient(options.OpenAI.Model)
                .AsAIAgent(instructions: instructions, name: "Architect"),

            ModelProvider.Anthropic => new AnthropicClient { ApiKey = options.Anthropic.ApiKey }
                .AsAIAgent(model: options.Anthropic.Model, name: "Architect", instructions: instructions),

            _ => throw new ArgumentOutOfRangeException(nameof(provider))
        };
    }
}
```

Both branches return `AIAgent`. That's the whole point of the phase.

## Step 5 — CLI loop with sessions + streaming

`apps/Cli/Program.cs`

```csharp
using Arkandia.Architect.Application.Agents;
using Arkandia.Architect.Infrastructure.Agents;
using Arkandia.Architect.Infrastructure.Providers;
using Microsoft.Agents.AI;
using Microsoft.Extensions.Configuration;

var config = new ConfigurationBuilder().AddUserSecrets<Program>().AddEnvironmentVariables().Build();
var options = new ProviderOptions();
config.GetSection("Providers").Bind(options);       // Providers:Default
config.GetSection("OpenAI").Bind(options.OpenAI);
config.GetSection("Anthropic").Bind(options.Anthropic);

var provider = Enum.Parse<ModelProvider>(options.Default, ignoreCase: true);
var docPath = args.Length > 0 ? args[0] : null;
var doc = docPath is null ? null : await File.ReadAllTextAsync(docPath);

IArchitectAgentFactory factory = new ArchitectAgentFactory(options);
AIAgent agent = factory.Create(provider, doc);
AgentSession session = await agent.CreateSessionAsync();

Console.WriteLine($"[{provider}] Architect ready. /save /load /exit");

while (true)
{
    Console.Write("\n> ");
    var input = Console.ReadLine();
    if (string.IsNullOrWhiteSpace(input)) continue;
    if (input == "/exit") break;

    if (input == "/save")
    {
        var json = agent.SerializeSession(session);
        await File.WriteAllTextAsync("session.json", json.ToString());
        Console.WriteLine("saved"); continue;
    }
    if (input == "/load")
    {
        var json = System.Text.Json.JsonSerializer.Deserialize<System.Text.Json.JsonElement>(await File.ReadAllTextAsync("session.json"));
        session = await agent.DeserializeSessionAsync(json);
        Console.WriteLine("loaded"); continue;
    }

    await foreach (var update in agent.RunStreamingAsync(input, session))
        Console.Write(update);
    Console.WriteLine();
}
```

> `SerializeSession` returns a `JsonElement`; check the [Session](https://learn.microsoft.com/agent-framework/concepts/agents/conversations/session) page for the exact shape when you write this — that's the most likely API to have shifted.

## Step 6 — Run with both providers

```bash
dotnet run --project apps/Cli -- docs/some-architecture.md
```

Ask the same 3 questions:

1. "Summarize the architecture in 5 bullets."
2. "What does the document say about the dependency direction?"
3. "What is NOT covered in this document that an architect would expect?"

Then `/exit`, flip `Providers:Default` to `anthropic`, repeat. Paste both outputs into the PR.

Test session persistence: ask "My name is X", `/save`, restart, `/load`, ask "What's my name?".

## Step 7 — Tests (minimal)

`tests/Arkandia.Architect.Agents.Tests`: one test that `ArchitectAgentFactory.Create` returns an `AIAgent` for each provider without hitting the network (construction only). Enough for v0.1.

## Step 8 — ADR-001

Copy `docs/templates/ADR-template.md` → `docs/architecture/adr/ADR-001-why-microsoft-agent-framework.md`.

Must answer:
- Why MAF over LangChain / Deep Agents for this project (C#, Azure, Foundry, unified SK+AutoGen successor).
- Why not raw `Microsoft.Extensions.AI` alone (what `AIAgent`/`AgentSession`/workflows add).
- Where the provider seam is (`IChatClient` → `AIAgent`) and what differed between OpenAI and Anthropic in this phase (history management).

## Step 9 — Ship

```bash
git add -A && git commit -m "v0.1: architecture chat with AIAgent, sessions, OpenAI + Anthropic"
git push -u origin feature/v0.1-architecture-chat
# open PR (template auto-loads from .github/) → merge
git checkout main && git pull
git tag v0.1.0 && git push --tags
# GitHub Release v0.1.0: notes = what surprised you (5 lines)
```

## Expected repo state after v0.1

```
arkandia-architect-agents/
├── ArkandiaArchitect.sln
├── global.json
├── Directory.Build.props
├── Directory.Packages.props
├── apps/Cli/
├── packages/
│   ├── Core/Domain/
│   ├── Core/Application/
│   ├── Agents/                 (may be empty shell in v0.1 — factory lives in Infrastructure until v0.2 splits instructions out)
│   └── Infrastructure/
├── tests/Arkandia.Architect.Agents.Tests/
└── docs/architecture/adr/ADR-001-why-microsoft-agent-framework.md
```

## Before v0.2

Write `roadmap/v0.2-repository-intelligence/lab.md` against current docs:
[Function tools](https://learn.microsoft.com/agent-framework/agents/tools/function-tools), [Tool approval](https://learn.microsoft.com/agent-framework/agents/tools/tool-approval), [Hosting](https://learn.microsoft.com/agent-framework/get-started/hosting).
