# Legacy AI Orchestrator

A cost-controlled GitHub Copilot agent collection that assigns expensive reasoning to a strong model and high-volume evidence work to GPT-5.6 Luna.

## Two workflows

### Legacy Architect

Use for full analysis and modernization planning of large legacy systems.

- The strong model orchestrates and makes architecture decisions.
- Luna inventories and analyzes modules in isolated contexts.
- Detailed findings are stored under `docs/legacy-analysis/`.

### Hybrid Builder

Use for normal feature development, bug fixes, and refactoring.

- The strong model decides what must be researched.
- Luna performs repository search, dependency tracing, web research, read-only database queries, terminal commands, builds, tests, and diagnostics.
- The strong model reads only the exact target files, makes the final decision, and writes the production code itself.
- Luna verifies the completed change but never edits application code.

## Architecture

```text
User
  |
  v
Strong model - GPT-5.6 Sol or Claude Opus
  |
  +-- Luna evidence worker(s)
  |     +-- code search and tracing
  |     +-- web and documentation research
  |     +-- read-only database queries
  |     +-- commands, builds, tests, and diagnostics
  |
  v
Strong model decides and writes code
  |
  v
Luna runs focused verification
```

## Repository contents

```text
.github/agents/
├── legacy-architect.agent.md
├── luna-codebase-analyst.agent.md
├── hybrid-builder.agent.md
└── luna-research-worker.agent.md

prompts/
├── analyze-legacy-codebase.prompt.md
└── build-with-luna-research.prompt.md

.vscode/settings.example.json
docs/legacy-analysis/.gitkeep
```

## Installation

Copy the required `.agent.md` pair into the target repository under `.github/agents/`.

For legacy system analysis:

- `legacy-architect.agent.md`
- `luna-codebase-analyst.agent.md`

For coding with Luna research:

- `hybrid-builder.agent.md`
- `luna-research-worker.agent.md`

Open the target repository in VS Code with GitHub Copilot Chat and select the parent agent from the agent picker.

## Model setup

Workers are configured for GPT-5.6 Luna. Set the parent agent's `model` field to the exact identifier shown by your VS Code model picker, such as GPT-5.6 Sol or Claude Opus.

Model identifiers can vary by Copilot entitlement and VS Code release. Prefer the value inserted by the VS Code model picker.

## Hybrid Builder cost controls

- Luna receives only bounded, decision-relevant research questions.
- Trivial tasks skip subagent research when all required context is already known.
- Luna returns compact JSON rather than raw logs, complete files, or long reports.
- The parent has no broad search, web, database, terminal, build, or test tools.
- The parent writes the code so expensive reasoning is used where implementation quality matters.
- Independent Luna research tasks can run in parallel, with a recommended maximum of three.
- Normal work allows one follow-up research round and two fix-and-verify loops.
- High-risk financial, security, schema, migration, concurrency, retry, and contract changes require stronger evidence and independent verification.

## Database and MCP tools

The Luna worker includes common read, search, command, and web tools. Database access requires a database MCP server or a safe command-line client configured in VS Code.

Add the exact MCP tool or tool set exposed by your environment to the Luna worker's `tools` list when needed, for example:

```yaml
tools:
  - read
  - search
  - execute
  - web
  - your-database-mcp/*
```

Database access is read-only by default. Use `SELECT`, metadata inspection, sanitized aggregates, and `EXPLAIN`. Do not allow DDL, DML, administrative changes, or unnecessary production row extraction without explicit approval.

## Generated legacy-analysis artifacts

```text
docs/legacy-analysis/
├── inventory.json
├── modules/
├── verification/
├── system-spec.md
├── modernization-options.md
└── decision-packet.json
```

## Safety

Use read-only access for databases, SharePoint, Confluence, and other external systems during research. Never expose secrets, credentials, customer records, card data, or production personal data to agent output.

A clean-slate rewrite should not be approved until behavioral completeness, reconciliation, audit requirements, failure recovery, parallel-run strategy, and rollback have evidence.

## License

MIT
