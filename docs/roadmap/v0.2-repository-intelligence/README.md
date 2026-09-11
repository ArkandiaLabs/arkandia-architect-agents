# v0.2 — Repository Intelligence

**Vehicle:** the architect can inspect a local repo: list projects, read files, find project references, grep for patterns.

## MAF concepts (the goal)

- Function tools: `AIFunctionFactory.Create`, `[Description]` on methods/params
- Tool approval: `ApprovalRequiredAIFunction`, `ToolApprovalRequestContent`, resuming a run with the approval
- Tools with DI: tools that depend on services (file system abstraction, repo root)
- Hosting: `Microsoft.Agents.AI.Hosting` — `AddAIAgent`, `WithAITool`, first `apps/Api` endpoint
- Tool events in streaming (see tool calls happen)

## Done when

- [ ] 4–5 tools in `packages/Tools`: `ListProjects`, `ReadFile`, `FindProjectReferences`, `Grep`, `ListDirectory`
- [ ] Tools are plain file/glob/regex — **no Roslyn, no MSBuild**
- [ ] One tool (e.g. `ReadFile` outside repo root, or a hypothetical `WriteFile`) requires approval; CLI prompts y/n and resumes
- [ ] `apps/Api` hosts the agent via `AddAIAgent` with a POST endpoint; `apps/Cli` still works
- [ ] "Explain the dependency flow of this solution" produces a correct answer on `arkandia-architect-agents` itself, with both providers
- [ ] Tool calls visible in the stream

## Out of scope

- Roslyn / semantic analysis
- Git history tools
- Caching, indexing, embeddings
- Auth on the API

## Build

1. `Tools`: static or instance methods with `[Description]`; register via `AIFunctionFactory.Create`.
2. Repo root as a scoped service; tools reject paths outside it.
3. Wrap one tool in `ApprovalRequiredAIFunction`; handle `ToolApprovalRequestContent` in CLI loop.
4. `Api`: `builder.AddAIAgent("architect", ...).WithAITool(...)`; minimal endpoint.
5. Prompt set: 5 questions about your own repo; run on both providers; note tool-call differences (which tools, how many calls).

## Provider notes

- Tool approval is framework-level (function-invoking chat client) → works on both.
- Watch for parallel tool calls differences between providers.

## Git

- Branch `feature/v0.2-repository-intelligence`
- Release `v0.2.0`
- **ADR-002: Monorepo with `apps/` + `packages/`** (vs `src/`), PascalCase, dependency rule, and Central Package Management (`Directory.Build.props` / `Directory.Packages.props`) as the answer to prerelease churn.

## Resources

- [Step 2 — Add tools](https://learn.microsoft.com/agent-framework/get-started/add-tools)
- [Function tools](https://learn.microsoft.com/agent-framework/agents/tools/function-tools)
- [Tool approval (human in the loop)](https://learn.microsoft.com/agent-framework/agents/tools/tool-approval)
- [Step 7 — Host your agent (ASP.NET Core)](https://learn.microsoft.com/agent-framework/get-started/hosting)
- [Tools overview](https://learn.microsoft.com/agent-framework/agents/tools/)

## Stretch

- Structured output: return findings as a typed record (`GetResponseAsync<T>` pattern).
- Function-invocation middleware that logs every tool call with args + duration.

Lab: not written yet — write `lab.md` against current MS Learn docs before starting this phase.
