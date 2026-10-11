---
name: maf-workflows
description: Builds and runs Microsoft Agent Framework workflows for PMCR-O role graphs with WorkflowBuilder, executors, and JSON checkpoints. Use when wiring role executors, adding checkpoints, or resuming a workflow in .NET. Do not use for agent tool wiring (use maf-mcp-tools) or Ollama client setup (use maf-ollama-agents).
---

# maf-workflows

Use a custom `WorkflowBuilder` graph for PMCR-O roles. Read `references/orchestration.md` before choosing a
built-in orchestration.

## When to Use

- Adding a role executor (planner, maker, checker, reflector) to a workflow
- Connecting executors with `AddEdge` and an output with `WithOutputFrom`
- Writing checkpoints with `CheckpointManager.CreateJson`
- Resuming a run from a checkpoint

## When Not to Use

- Choosing sequential, group-chat or handoff orchestration (read references first)
- Configuring the Ollama chat client (use maf-ollama-agents)
- Exposing tools or MCP servers (use maf-mcp-tools)

## Inputs

| Input | Required | Description |
| --- | --- | --- |
| Role list | Yes | Ordered roles and their executor types |
| Checkpoint directory | Yes | Where JSON checkpoints are written |
| Package version | Yes | Read from `Directory.Packages.props`. Do not assume |

## Workflow

1. Read the pinned `Microsoft.Agents.AI.Workflows` version. Check API names against that version.
2. Build the graph: `new WorkflowBuilder(first).AddEdge(a, b)...WithOutputFrom(last).Build()`.
3. Run with `InProcessExecution.RunStreamingAsync(workflow, input, checkpoints)`.
4. Checkpoints are written at each superstep. Store them with `CheckpointManager.CreateJson`.
5. To resume, use `RestoreCheckpointAsync` or `InProcessExecution.ResumeStreamingAsync`. The graph must match
   the checkpointed topology and executor identities.
6. Report the run id and the checkpoint path. Report failed executors from `ExecutorFailedEvent`.

## Validation

- [ ] The workflow builds without warnings about unused executors
- [ ] A checkpoint file exists after a run
- [ ] A resumed run reports the same executor identities as the checkpoint

## Common Pitfalls

| Pitfall | Solution |
| --- | --- |
| Using sequential orchestration for the checker | Built-in sequential passes full history. The checker must not see the maker conversation. Keep the custom graph |
| Executor state kept in fields | Put state in the message, or use `OnCheckpointingAsync` with `QueueStateUpdateAsync` |
| Calling resume APIs that the pinned version lacks | Check the pinned version first. Report the gap if it is missing |
| Assuming checkpoints resume across package upgrades | Not verified. Test a resume after any upgrade |
