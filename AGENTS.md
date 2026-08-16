# Vibe-Trading — Agent Navigation

## Quick Reference

| File | Purpose |
|------|---------|
| [`.github/copilot-instructions.md`](.github/copilot-instructions.md) | Architecture overview, build/test commands, security rules |
| [`AGENT_CONTRIBUTOR_GUIDE.md`](AGENT_CONTRIBUTOR_GUIDE.md) | Full contribution guide: DCO, safe checks, high-risk surfaces, PR expectations |
| [`CONTRIBUTING.md`](CONTRIBUTING.md) | Community contribution governance |
| [`SECURITY.md`](SECURITY.md) | Vulnerability reporting policy |

## Agent skills

### Issue tracker

Issues and specs live as markdown files under `.scratch/`. See `docs/agents/issue-tracker.md`.

### Triage labels

Five canonical labels: `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context — one `CONTEXT.md` at the repo root, ADRs in `docs/adr/`. See `docs/agents/domain.md`.

## Key Directories

| Path | Description |
|------|-------------|
| `agent/src/agent/` | ReAct loop, context management, tool registry |
| `agent/src/api/` | FastAPI routes (runs, sessions, live trading, options) |
| `agent/src/tools/` | 60+ financial tool implementations |
| `agent/src/providers/` | LLM provider adapters |
| `agent/src/trading/` | Broker connectors, mandates, order gate, audit ledger |
| `agent/src/backtest/` | Backtest engines (equity, options, perpetual futures) |
| `agent/src/factors/` | Alpha Zoo — 462 pre-built alpha factors |
| `frontend/src/` | React 19 + TypeScript SPA |

