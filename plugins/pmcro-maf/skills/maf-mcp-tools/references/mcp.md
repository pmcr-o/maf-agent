# MCP calls (MAF, .NET)

Packages: `ModelContextProtocol` (stdio, attributes), `ModelContextProtocol.AspNetCore` (HTTP),
`Microsoft.Agents.AI` (`AsAIAgent`, `AsAIFunction`).

## Consume an MCP server

```csharp
var tools = await mcpClient.ListToolsAsync();
var agent = chatClient.AsAIAgent(instructions: role, tools: tools.Cast<AITool>().ToList());
```

## Expose an agent as an MCP tool

```csharp
var tool = McpServerTool.Create(agent.AsAIFunction());
builder.Services.AddMcpServer().WithStdioServerTransport().WithTools([tool]);
```

## Serve local tools over HTTP

```csharp
builder.Services.AddMcpServer().WithHttpTransport().WithToolsFromAssembly();
app.MapMcp("/api/mcp");
```

Tools are methods marked `[McpServerTool]` on a class marked `[McpServerToolType]`.

## Not supported

- OpenAPI document to MCP server. Hand-write the tools.
- Foundry OpenAPI tool is a hosted Foundry feature, not local MCP.
