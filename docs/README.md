# Arkandia Architect — MAF Learning Kit

Learn **Microsoft Agent Framework (MAF)** on **.NET 10 / C#** by building a real product: an AI platform that understands software systems (architecture reviews, incident investigation).

This folder is the curriculum for this repo. The code lives alongside it (`apps/`, `packages/`), built phase by phase.

**Repo:** `arkandia-architect-agents` (Arkandia org)
**Description:** Multi-agent platform on Microsoft Agent Framework: architecture review + incident intelligence
**Topics:** `microsoft-agent-framework` `dotnet` `csharp` `ai-agents` `multi-agent` `mcp` `azure-ai-foundry` `clean-architecture`

## Principles

1. **Concept first, feature second.** Every phase names the MAF concept you're learning; the feature is only the vehicle.
2. **No calendar.** A phase is done when its "Done when" list is green.
3. **Both providers, no versus.** OpenAI and Anthropic are used side by side through `Microsoft.Extensions.AI`. The goal is provider-agnostic design, not benchmarking.
4. **Real Azure.** Foundry phase runs on a real subscription, both tracks (build in Foundry / host MAF in Foundry).
5. **Run it like a product.** Issue → branch → PR → Release → ADR, every phase.
6. **Ship the decision, not just the code.** Each phase ends with an ADR explaining *why*.

## Layout

```
docs/
├── README.md                      ← you are here: index, principles, why MAF
├── study-plan.md                  the method: per-phase ritual + guardrails (read once, applies to every phase)
├── architecture/                  reference — read on demand
│   ├── architecture.md            product, workspaces, repo layout, dependency rule, build files
│   ├── providers.md               OpenAI + Anthropic through Microsoft.Extensions.AI
│   └── adr/                       ADR-00N-*.md, one per release
├── roadmap/                       the journey
│   ├── README.md                  phase table + dependency map
│   ├── v0.1-architecture-chat/
│   │   ├── README.md              phase guide: goal, done-when, out-of-scope, links
│   │   └── lab.md                 step-by-step
│   ├── v0.2-…/ … v1.0-hardening/  README.md each; lab.md written just before starting the phase
│   └── v0.7-foundry/
│       └── foundry-guide.md       Foundry background (two tracks, vocabulary, prereqs)
└── templates/                     ADR + phase-guide templates (issue/PR templates live in .github/)
```

Hierarchy: `roadmap/README.md` → `roadmap/vX.Y-*/README.md` → `roadmap/vX.Y-*/lab.md`.

## Reading order

1. This README
2. [study-plan.md](study-plan.md)
3. [roadmap/README.md](roadmap/README.md)
4. [architecture/architecture.md](architecture/architecture.md)
5. [architecture/providers.md](architecture/providers.md)
6. [roadmap/v0.7-foundry/foundry-guide.md](roadmap/v0.7-foundry/foundry-guide.md) — skim now; read properly before v0.7

Then per phase: `roadmap/vX.Y-*/README.md` → `lab.md` → `architecture/adr/ADR-00N-*.md`.

## How to use

1. Read 1–5 above once.
2. Open [roadmap/v0.1-architecture-chat/README.md](roadmap/v0.1-architecture-chat/README.md) → create the GitHub issue (Phase template).
3. Follow [roadmap/v0.1-architecture-chat/lab.md](roadmap/v0.1-architecture-chat/lab.md).
4. PR → merge → Release `v0.1.0` → `architecture/adr/ADR-001-*.md`.
5. Before v0.2: write `roadmap/v0.2-repository-intelligence/lab.md` against **current** MS Learn docs (`.mcp.json` wires the Microsoft Learn MCP server for that).

## Source of truth

All links point to Microsoft Learn. Re-verify APIs when you start each phase — core MAF is GA, but several integration packages (Anthropic, AG-UI, Foundry hosting) still ship as `--prerelease` and move.
