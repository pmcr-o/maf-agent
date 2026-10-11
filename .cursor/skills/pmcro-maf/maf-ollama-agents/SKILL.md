---
name: maf-ollama-agents
description: Creates a Microsoft Agent Framework agent backed by a local Ollama model through OllamaSharp, sized for a 16,384-token context. Use when configuring the Ollama endpoint, model name, timeouts, or AsAIAgent for PMCR-O roles. Do not use for workflow graph wiring (use maf-workflows) or MCP tools (use maf-mcp-tools).
---

# maf-ollama-agents

Wrap an `OllamaApiClient` as an agent. Read `references/local-model-budget.md` before adding prompts or tools.

## When to Use

- Creating an agent from `OllamaApiClient` with `AsAIAgent`
- Setting the endpoint and model name
- Setting HTTP timeouts for long local inference
- Checking whether the model supports tool calls

## When Not to Use

- Building the workflow graph (use maf-workflows)
- Exposing or consuming MCP tools (use maf-mcp-tools)
- Foundry Local in .NET. Learn says it is not supported

## Inputs

| Input | Required | Description |
| --- | --- | --- |
| Endpoint | Yes | Base URI, for example `http://localhost:11434`. Strip any `Endpoint=` prefix from config |
| Model | Yes | Model tag, for example `qwen3:8b` |
| Role prompt | Yes | System prompt for the role. Keep within budget |

## Workflow

1. Create the client: `new OllamaApiClient(new Uri(endpoint), model)`. Set `HttpClient.Timeout` to `Timeout.InfiniteTimeSpan` for local inference.
2. Create the agent: `client.AsAIAgent(instructions: rolePrompt, name: roleName)`.
3. Run once with a short prompt to confirm the model responds.
4. Before adding tools, test one tool call. Not every local model supports function calling.
5. Report the model tag and the endpoint used. Never report success from a failed call.

## Validation

- [ ] The agent returns text for a short prompt
- [ ] The role prompt plus conversation fits in 16,384 tokens
- [ ] Tool calls tested on the chosen model before use

## Common Pitfalls

| Pitfall | Solution |
| --- | --- |
| Default 100-second HTTP timeout cuts off inference | Set infinite timeout on the HttpClient |
| Prompt exceeds the context window | Move detail to references and load only what the role needs |
| Model fails on tools | Pick a model that supports function calling, or drop tools for that role |
