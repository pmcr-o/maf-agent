---
name: maf-mcp-tools
description: Connects Microsoft Agent Framework agents to MCP servers, or exposes agents and ASP.NET endpoints as MCP tools, using the official ModelContextProtocol C# SDK. Use when listing MCP tools, passing them to AsAIAgent, or serving tools with MapMcp. Do not use for workflow graphs (use maf-workflows) or for OpenAPI-to-MCP generation, which MAF does not provide.
---

# maf-mcp-tools

Read `references/mcp.md` for the exact calls. Keep MCP servers as separate processes, not inside the orchestrator.

## When to Use

- Consuming an MCP server: list its tools and pass them to an agent
- Exposing an agent as an MCP tool
- Serving local tools over HTTP with `MapMcp`

## When Not to Use

- Wiring the workflow graph (use maf-workflows)
- Creating the Ollama client (use maf-ollama-agents)
- Converting an OpenAPI document into MCP tools. Not supported by MAF. Write tool methods by hand

## Inputs

| Input | Required | Description |
| --- | --- | --- |
| Server | Yes | Command (stdio) or URI (HTTP) of the MCP server |
| Tool allowlist | Yes | Names from `tools/allowlist.json` only |

## Workflow

1. Check the server against `tools/allowlist.json`. Stop if the server is not listed.
2. Connect with the MCP client and call `ListToolsAsync()`.
3. Cast the tools to `AITool` and pass them to `AsAIAgent(..., tools:)`.
4. To serve: `AddMcpServer().WithHttpTransport().WithToolsFromAssembly()` then `MapMcp()`.
5. Run one tool call and report its result or error. A failed tool is a result, not a success.

## Validation

- [ ] Every tool used is on the allowlist
- [ ] `ListToolsAsync` returns at least one tool
- [ ] One call returns output that the agent used

## Common Pitfalls

| Pitfall | Solution |
| --- | --- |
| Running a Playwright or terminal MCP inside the orchestrator | Keep them as separate AppHost projects so a crash does not stop the orchestrator |
| Expecting OpenAPI to produce MCP tools | Not supported. Write `[McpServerTool]` methods |
| Tool not on the allowlist | Stop and report it. Do not add it |
