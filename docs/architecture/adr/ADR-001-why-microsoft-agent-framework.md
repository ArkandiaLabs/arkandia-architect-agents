# ADR-001: Why Microsoft Agent Framework

- **Status:** Draft — complete during v0.1
- **Phase:** v0.1
- **Date:** 2026-09-11

## Context

Arkandia Architect needs an agent framework for a .NET 10 product targeting enterprise / Azure customers, using OpenAI and Anthropic models through one abstraction, with a path to Microsoft Foundry.

Adoption signals considered:

| Signal | State (Sep 2026) |
|---|---|
| Maturity | MAF 1.0 GA 2026-04 (.NET + Python). Harness + Foundry Hosted Agents GA 2026-06. Semantic Kernel + AutoGen in maintenance — MAF is the single path. |
| Production use | Microsoft-published: State Farm (41 prod builds, 3,000+ agents), Atos, Chow Tai Fook (400+ agents), Citrix. Regulated-industry, Azure-first. |
| Community | GitHub ~15k stars vs LangGraph ~32k. |
| Jobs | Not yet ranked in agentic job listings (LangChain 392, LangGraph 256, LlamaIndex 150, CrewAI 106, AutoGen 74). |

## Decision

<!-- complete in v0.1: MAF over LangChain/Deep Agents; MAF over raw Microsoft.Extensions.AI; where the provider seam is -->

## Alternatives considered

| Option | Pros | Cons | Why not |
|---|---|---|---|
| LangGraph / Deep Agents (Python) | largest community, jobs, proven at scale | Python-first; no Foundry/Azure identity story; off-target for .NET enterprise audience | |
| Raw `Microsoft.Extensions.AI` | minimal, stable | no sessions, workflows, harness, hosting adapters | |
| Semantic Kernel | mature .NET | maintenance mode | |

## Consequences

<!-- complete in v0.1 -->

## What I learned

<!-- complete in v0.1 -->

## Provider notes

<!-- complete in v0.1: history management differences OpenAI Responses vs Anthropic -->

## Sources

- [MAF 1.0 GA](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/the-future-of-agentic-ai-inside-microsoft-agent-framework-1-0/4510698)
- [Harness + Hosted Agents GA (InfoQ)](https://www.infoq.com/news/2026/08/agent-framework-harness-ga/)
- [Microsoft adoption stories](https://adoption.microsoft.com/en-us/ai-agents/transformation-stories/)
- [Agentic job market 2026](https://agentic-engineering-jobs.com/ai-agent-frameworks-job-market-2026)
- [GitHub rankings May 2026](https://presenc.ai/research/ai-agent-framework-github-rankings-2026)
