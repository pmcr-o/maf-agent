# Orchestration choices

Research basis: W:\skills research notes, 7 October 2026. Re-check against the pinned package version.

| Pattern | Context an agent receives | Fit for PMCR-O |
| --- | --- | --- |
| Custom `WorkflowBuilder` graph | Only what the edge passes | Yes. Keeps checker isolation |
| `AgentWorkflowBuilder.BuildSequential` | Full previous conversation by default | No, unless context is narrowed |
| Group chat (`RoundRobinGroupChatManager`) | Shared conversation | No |
| Handoff (`CreateHandoffBuilderWith`) | Transfers control between agents | No |
| Magentic | Manager plans and delegates | Not for fixed cycles |

## Checkpoint facts

- Checkpoints are taken at the end of each superstep.
- They hold executor state, pending messages, pending requests and shared state.
- Stable executor identity is required for resume.
- The JSON store is `CheckpointManager.CreateJson(new FileSystemJsonCheckpointStore(dir))`.
- Durable distributed runs use a separate package (`Microsoft.Agents.AI.DurableTask`). It is not in this marketplace.

## Version note

NuGet lists `Microsoft.Agents.AI.Workflows` 1.23.0 (29 September 2026). ProjectName is pinned to 1.17.0.
Confirm which API exists in the pinned version before writing code.
