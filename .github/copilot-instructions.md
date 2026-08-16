# Vibe-Trading — Agent Instructions

## Codebase Memory MCP

**MANDATORY: use Codebase Memory MCP graph tools FIRST — before reading files or making code changes.**

This rule applies to every request involving this codebase.

Always call `list_projects` first when you do not already know the project name, then use the `display_name` or exact `name` returned by that tool.

```json
// Step 0 — discover project names
mcp_codebase-memo_list_projects()

// Step 1 — use the project identifier returned above
mcp_codebase-memo_get_architecture({ "project": "<display_name>" })
```

### Workflow

1. Call `list_projects` to discover the correct project name.
2. Call `get_architecture(project)` to understand the codebase structure.
3. Use `search_graph` to find relevant symbols, `trace_call_path` for call chains.
4. Use `get_code_snippet` to read specific function implementations.
5. Only use `read_file` when you need exact raw content to edit a specific line.

### Available Tools (14 MCP tools)

**Indexing:**
- `index_repository(repo_path)` — Index a repository into the knowledge graph
- `list_projects` — List all indexed projects with node/edge counts
- `delete_project(project)` — Remove a project and all its graph data
- `index_status(project)` — Check indexing status

**Querying:**
- `search_graph(name_pattern, name_scope, label, file_pattern, exclude_file_pattern)` — Structured search by label, name/qualified_name, include/exclude file globs
- `trace_call_path(function_name, direction, depth)` — BFS call chain traversal
- `detect_changes(project)` — Map git diff to affected symbols + risk
- `query_graph(query)` — Execute Cypher-like graph queries (read-only)
- `get_graph_schema(project)` — Node/edge counts, relationship patterns
- `get_code_snippet(qualified_name)` — Read source code for a function
- `get_architecture(project)` — Codebase overview: languages, packages, routes, hotspots
- `search_code(pattern, project)` — Grep-like text search within indexed files
- `manage_adr(action)` — CRUD for Architecture Decision Records
- `ingest_traces(traces)` — Ingest runtime traces to validate HTTP edges

---

## Architecture Overview

### Repository Layout

| Path | Purpose |
|------|---------|
| `agent/` | Python backend package (main source code) |
| `agent/src/agent/` | ReAct loop, context management, tool registry |
| `agent/src/api/` | FastAPI route modules (runs, sessions, live, options, etc.) |
| `agent/src/tools/` | 60+ tool implementations (market data, backtest, trading, etc.) |
| `agent/src/providers/` | LLM provider adapters (OpenAI, DeepSeek, Anthropic, Copilot) |
| `agent/src/trading/` | Broker connectors, mandate enforcement, order gate, audit ledger |
| `agent/src/backtest/` | 9 backtest engines (equity, options, perpetual futures) |
| `agent/src/factors/` | Alpha Zoo — 462 pre-built alpha factors |
| `agent/src/shadow_account/` | Shadow account: extract → backtest → report |
| `agent/src/swarm/` | Multi-agent orchestration |
| `api_server.py` | FastAPI application entry point |
| `mcp_server.py` | MCP protocol entry point (~70 read-only financial tools) |
| `cli/` | CLI entry point (`vibe-trading` command) |
| `frontend/` | React 19 + Vite + TypeScript SPA (port 5899) |
| `wiki/` | Public documentation site (separate deployment) |

### Key Conventions

- **Tool naming**: `snake_case_tool_name`, descriptions used for LLM prompting
- **Frontend path alias**: `@/` → `src/`
- **Agent context layers**: L1 microcompact → L5 iterative update (see `src/agent/loop.py`)
- **Read-only MCP tools**: All MCP-exposed tools set `is_readonly = True`
- **DCO required**: Community PRs must use `git commit -s` with `Signed-off-by:` trailer

---

## Build & Test Commands

See [AGENT_CONTRIBUTOR_GUIDE.md](../AGENT_CONTRIBUTOR_GUIDE.md) for full details.

### Python (backend)

```bash
pip install -e ".[dev]"                          # install deps
python -m py_compile agent/api_server.py agent/mcp_server.py  # syntax check
pytest --ignore=agent/tests/e2e_backtest --ignore=agent/tests/test_e2e_harness_v2.py --tb=short -q
```

### Frontend

```bash
cd frontend && npm ci && npm run build   # type-check + build
cd frontend && npm test                  # Vitest watch
```

### Docker

```bash
docker compose up -d    # full stack on localhost:8899
```

---

## Security & Safety Boundaries

- **NEVER commit** `.env` files, broker tokens, API keys, or private trading data
- **Broker/order writes** are safety-critical — get approval before modifying
- **Live trading flows** must never run during routine PR validation
- **External deployments** beyond loopback require `API_AUTH_KEY`
- Treat any exposed secret as access, not text — redact and recommend rotation

