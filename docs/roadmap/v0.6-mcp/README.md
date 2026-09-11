# v0.6 — MCP Ecosystem

**Vehicle:** replace hand-written integrations with MCP servers (filesystem, GitHub, Microsoft Learn) and expose Arkandia Architect itself as an MCP server.

## MAF concepts (the goal)

- Local MCP tools: `McpClientFactory.CreateAsync(StdioClientTransport)` → `ListToolsAsync()` → tools into `AIAgent`
- Hosted MCP tools (provider executes the MCP call): `MCPToolDefinition`-style hosted tools where the provider supports it
- Expose an agent as an MCP server: `agent.AsAIFunction()` → `McpServerTool` (MCP C# SDK)
- Security posture: headers/credentials per server, allowed tools, disposal (`await using`)

## Done when

- [ ] Filesystem MCP server replaces `ReadFile`/`ListDirectory` tools (keep yours behind a flag for comparison)
- [ ] GitHub MCP server adds PR/issue context to Architecture Review
- [ ] Microsoft Learn MCP (`https://learn.microsoft.com/api/mcp`) available as hosted MCP on the provider(s) that support it, local MCP on the others
- [ ] `apps/Api` also runs as an MCP server exposing `review_repository` and `investigate_incident`; VS Code / Claude Code can call it
- [ ] Allowed-tools list per server; nothing runs unapproved that mutates state
- [ ] Both providers

## Out of scope

- Writing your own general-purpose MCP servers (beyond exposing the agent)
- OAuth flows for remote MCP servers
- Azure DevOps / Notion / Slack servers (backlog)

## Build

1. `Mcp`: `McpServerRegistry` — config-driven list of stdio/HTTP servers, allowed tools.
2. Compose tools: MCP tools + your remaining function tools into the same agent.
3. `Api`: MCP server host (`ModelContextProtocol` + `Microsoft.Extensions.Hosting`).
4. Test from an MCP client (Claude Code / VS Code) → document in `docs/assets`.

## Provider notes

- Hosted MCP: OpenAI Responses ✅, Anthropic ✅. Local MCP: both ✅. Record which path each provider took.

## Git

- Branch `feature/v0.6-mcp`
- Release `v0.6.0`
- **ADR-006: MCP instead of direct integrations** — cost of the protocol vs. cost of N bespoke tool sets; trust boundary.

## Resources

- [Using MCP tools with agents (local)](https://learn.microsoft.com/agent-framework/agents/tools/local-mcp-tools)
- [Hosted MCP tools](https://learn.microsoft.com/agent-framework/agents/tools/hosted-mcp-tools)
- [Expose agent as MCP tool (hosting)](https://learn.microsoft.com/agent-framework/hosting/self-hosting/mcp)
- [Get started with .NET AI and MCP](https://learn.microsoft.com/dotnet/ai/get-started-mcp)
- [MCP C# SDK](https://github.com/modelcontextprotocol/csharp-sdk)
- [.NET sample: Agent_MCP_Server](https://github.com/microsoft/agent-framework/tree/main/dotnet/samples/02-agents/ModelContextProtocol/Agent_MCP_Server)

Lab: not written yet — write `lab.md` against current MS Learn docs before starting this phase.
