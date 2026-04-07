# Technical PM

Multi-agent Technical PM skill for AI coding agents. Coordinates coding tasks across multiple agents (local LLMs, OpenCode, Codex, Claude) with cost-optimized dispatch, mandatory review chains, and a standardized task pipeline.

## Install

```bash
npx skills add htuzel/technical-pm
```

## What it does

- Acts as a **Technical PM** that coordinates multi-agent coding workflows
- Dispatches tasks to the cheapest suitable agent (local models first, Anthropic last)
- Enforces a **5-step pipeline**: Code → Review → Test → Manual QA → Merge
- Ensures writer != reviewer for every code change
- Runs independent tasks in parallel via `opencode_fire`

## Agent Lineup (It depends on your AI Subscriptions/Local Machine Capability, also may need skills.sh file change) 

| Level | Agent | Cost |
|-------|-------|------|
| Junior | Gemma-4-31b-it | Free (local) |
| Intermediate | Qwen3-Coder | Free (local) |
| Intermediate | MiniMax-M2.7 / Qwen3.6 Plus | Low |
| Senior | GPT 5.4 Codex | Medium |
| Senior | Sonnet 4.6 | High |
| Principal | Opus 4.6 | Highest |

## Supported Agents

Works with Claude Code, OpenCode, Codex CLI, and any agent that supports the skills standard.

## License

MIT
