# v1.0 — Hardening

**Vehicle:** make it a reference implementation someone else can run.

## MAF concepts (the goal)

- Session persistence: `SerializeSession` / `DeserializeSessionAsync`, storage strategies, hosted session store (`WithInMemorySessionStore` → your own)
- Evaluation: `LocalEvaluator` + `EvalChecks` (keyword, tool-called), `FunctionEvaluator`, MEAI evaluators, `FoundryEvals`; conversation split strategies; quality gates in CI
- Observability end-to-end: OTel from `IChatClient` → agent → workflow → API; Aspire dashboard; optional App Insights
- Packaging: Docker, `docker compose` (Api + IncidentLab + Aspire dashboard), .NET Aspire AppHost

## Done when

- [ ] Sessions survive API restart (file or SQLite store behind an `Application` port)
- [ ] `tests/Evals`: 10 golden questions per workspace; local checks run in CI; Foundry evals run on demand
- [ ] One trace shows the full path: HTTP → workflow → agent → tool → model
- [ ] `docker compose up` runs everything; README quickstart works from a clean clone
- [ ] Both providers configurable via env in containers

## Out of scope

- Kubernetes, IaC beyond `azd`
- Auth / RBAC
- Cost dashboards

## Build

1. `Infrastructure`: `FileSessionStore : ISessionStore`; wire into hosting.
2. `tests/Evals` project with `agent.EvaluateAsync(...)`.
3. Aspire AppHost referencing Api + IncidentLab; OTel exporters.
4. Dockerfiles; compose; README.

## Git

- Branch `feature/v1.0-hardening`
- Release `v1.0.0`
- **ADR-009: Production posture** — what "production-ready" means here and what was deliberately left out.

## Resources

- [Session](https://learn.microsoft.com/agent-framework/concepts/agents/conversations/session)
- [Storage](https://learn.microsoft.com/agent-framework/concepts/agents/conversations/storage)
- [Evaluation](https://learn.microsoft.com/agent-framework/agents/evaluation)
- [Foundry evaluation (MAF)](https://learn.microsoft.com/agent-framework/integrations/by-component/evaluation/microsoft-foundry)
- [Observability](https://learn.microsoft.com/agent-framework/agents/observability)
- [Hosting in ASP.NET Core](https://learn.microsoft.com/agent-framework/get-started/hosting)
- [.NET Aspire](https://learn.microsoft.com/dotnet/aspire/)

Lab: not written yet — write `lab.md` against current MS Learn docs before starting this phase.
