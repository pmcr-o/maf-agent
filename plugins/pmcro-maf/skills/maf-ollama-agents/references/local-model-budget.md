# Local model budget

Values from ProjectName's AppHost and OllamaExtensions.

- Model: `qwen3:8b`, set by `PMCRO_WORKER_MODEL` and `Ollama:Models:Orchestrator`
- Context: `OLLAMA_CONTEXT_LENGTH=16384`
- Flash attention: `OLLAMA_FLASH_ATTENTION=0`. It caused a SIGSEGV on an RTX 4070 Laptop GPU during model load
- HTTP timeout: infinite, so long inference is not cut off

## Budget per role

| Part | Share of 16,384 tokens |
| --- | --- |
| Role prompt | up to 2,000 |
| Skill text loaded | up to 4,000 |
| Conversation and input | up to 8,000 |
| Reserved for output | at least 2,000 |

Shares are a planning guide. Measure one real run before committing to them.
