# Legacy AI Orchestrator

A GitHub Copilot custom-agent collection for legacy analysis, strong-model/Luna teamwork, and high-autonomy coding with GPT-5.6 Luna.

## Workflows

### Luna Apex

Use when you want GPT-5.6 Luna itself to investigate, implement, test, review, and repair a task end-to-end.

- No subagents.
- No token-saving behavior.
- Persistent completion loop inspired by Burke Holland's Beast Mode.
- Uses current VS Code custom-agent frontmatter.
- Requires planning for non-trivial work.
- Uses test-first bug fixing where practical.
- Runs static checks, focused tests, builds, wider regression checks, and final diff review.
- Performs an adversarial self-review after normal validation passes.
- Does not declare success without evidence.

Install:

```text
.github/agents/luna-apex.agent.md
```

Then select **Luna Apex** in GitHub Copilot Chat and use:

```text
prompts/luna-apex-start.prompt.md
```

For best results, select GPT-5.6 Luna with the highest appropriate reasoning effort in the VS Code model picker. Reasoning effort is selected in the client; it is not forced by an undocumented agent frontmatter field.

Research and design rationale:

```text
docs/luna-apex-design.md
```

Optional long-session settings:

```text
.vscode/settings.luna-apex.example.json
```

Auto-approve remains disabled by default. Enable it only in trusted repositories after understanding which commands the agent can execute.

### Legacy Architect

Use for full analysis and modernization planning of large legacy systems.

- The strong model orchestrates and makes architecture decisions.
- Luna inventories and analyzes modules in isolated contexts.
- Detailed findings are stored under `docs/legacy-analysis/`.

### Hybrid Builder

Use for feature development, bug fixes, and refactoring where a strong model writes code and Luna performs research and tool-heavy work.

- The strong model decides what must be researched.
- Luna performs repository search, dependency tracing, web research, read-only database queries, terminal commands, builds, tests, and diagnostics.
- The strong model reads exact target files, makes the final decision, and writes production code.
- Luna verifies the completed change but never edits application code.

## Architecture options

### Luna Apex

```text
User
  |
  v
GPT-5.6 Luna - Luna Apex
  |
  +-- understand and investigate
  +-- plan and implement
  +-- run tests and builds
  +-- adversarially review the diff
  +-- repair failures
  |
  v
Evidence-backed completion
```

### Strong model with Luna workers

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
├── luna-research-worker.agent.md
└── luna-apex.agent.md

prompts/
├── analyze-legacy-codebase.prompt.md
├── build-with-luna-research.prompt.md
└── luna-apex-start.prompt.md

docs/
├── legacy-analysis/
└── luna-apex-design.md

.vscode/
├── settings.example.json
└── settings.luna-apex.example.json
```

## Installation

Copy the required `.agent.md` files into the target repository under `.github/agents/`.

For Luna-only autonomous coding:

- `luna-apex.agent.md`

For legacy system analysis:

- `legacy-architect.agent.md`
- `luna-codebase-analyst.agent.md`

For strong-model coding with Luna research:

- `hybrid-builder.agent.md`
- `luna-research-worker.agent.md`

Open the target repository in VS Code with GitHub Copilot Chat and select the desired agent from the agent picker.

## Model setup

Luna agents are configured for `GPT-5.6 Luna (copilot)`. Parent agents should use the exact qualified identifier shown by the VS Code model picker, such as GPT-5.6 Sol or Claude Opus.

Model identifiers and capabilities can vary by Copilot entitlement, extension version, client, rollout, and enterprise policy.

## Database and MCP tools

The Luna Research Worker includes common read, search, command, and web tools. Database access requires a database MCP server or safe command-line client configured in VS Code.

Add the exact MCP tool or tool set exposed by your environment when needed, for example:

```yaml
tools:
  - read
  - search
  - execute
  - web
  - your-database-mcp/*
```

Database access is read-only by default. Use `SELECT`, metadata inspection, sanitized aggregates, and `EXPLAIN`. Do not allow DDL, DML, administrative changes, or unnecessary production-row extraction without explicit approval.

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
