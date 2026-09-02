# Legacy AI Orchestrator

A GitHub Copilot custom-agent collection for high-autonomy coding, multi-agent orchestration, legacy analysis, and strong-model/low-cost-model teamwork.

## Apex

**Apex** is the recommended general-purpose autonomous coding workflow.

It is model-neutral by identity and intentionally aggressive about completion quality. The reference configuration currently runs on `GPT-5.6 Luna (copilot)`, but the model is an implementation detail: when a better low-cost capable model appears, update the `model:` fields without renaming the agents.

Apex does **not** try to save tokens or credits through reduced effort. It gets its economic advantage only from the configured model while preserving a full engineering workflow: decomposition, selective parallelism, implementation, execution, independent verification, adversarial review, and repair loops.

### Architecture

```text
                         User
                           |
                           v
                    +-------------+
                    |    Apex     |
                    | Orchestrator|
                    +------+------+ 
                           |
       +-------------------+-------------------+
       |                   |                   |
       v                   v                   v
 Apex Explorer      Apex Researcher    Apex Database Worker
       |                   |                   |
       +-------------------+-------------------+
                           |
                         evidence
                           |
                           v
                         Apex
                           |
              scoped, non-overlapping work
                           |
             +-------------+-------------+
             |                           |
             v                           v
     Apex Implementer A          Apex Implementer B
             |                           |
             +-------------+-------------+
                           |
                           v
                 Apex Terminal Worker
                           |
                           v
                    Apex Verifier
                           |
                           v
                    Apex Reviewer
                           |
                   pass / repair loop
                           |
                           v
                    Evidence-backed result
```

### Apex agents

```text
.github/agents/
├── apex.agent.md
├── apex-explorer.agent.md
├── apex-researcher.agent.md
├── apex-database-worker.agent.md
├── apex-terminal-worker.agent.md
├── apex-implementer.agent.md
├── apex-verifier.agent.md
└── apex-reviewer.agent.md
```

Only **Apex** is user-invocable. The specialized workers are hidden and are called by Apex through the Copilot subagent mechanism.

### Worker responsibilities

- **Apex Explorer** — repository search, dependency tracing, existing patterns, tests, configuration, and architecture evidence.
- **Apex Researcher** — current official documentation, standards, release notes, upstream source, issues, and external technical evidence.
- **Apex Database Worker** — read-only schema, metadata, query plans, stored procedures, and sanitized database evidence.
- **Apex Terminal Worker** — builds, tests, linters, diagnostics, logs, runtime checks, and command evidence.
- **Apex Implementer** — bounded production-code or test edits with explicit write ownership.
- **Apex Verifier** — independent acceptance-criteria verification after implementation.
- **Apex Reviewer** — fresh-context adversarial final review for correctness, security, architecture, edge cases, compatibility, and regression risk.

Independent work can run in parallel. Parallel implementers are allowed only for genuinely disjoint write scopes.

### Project installation

Copy the Apex files into a repository under:

```text
.github/agents/
```

Then select **Apex** in GitHub Copilot Chat. For complex work, start with:

```text
prompts/apex-start.prompt.md
```

Design rationale:

```text
docs/apex-design.md
```

Research findings and phased implementation roadmap:

[luna-premium-agent-system.md](docs/luna-premium-agent-system.md)

Optional long-session settings:

```text
.vscode/settings.apex.example.json
```

### Global installation

To make Apex available across projects for your user account, copy the version-controlled Apex suite to Copilot's user-level agent directory:

```bash
mkdir -p ~/.copilot/agents
cp .github/agents/apex*.agent.md ~/.copilot/agents/
```

The user-level location is:

```text
~/.copilot/agents/
```

Re-copy the files after repository updates if you want the global installation to stay in sync.

### Model policy

The current Apex files pin:

```text
GPT-5.6 Luna (copilot)
```

The agent and worker names intentionally contain no model name. When another model becomes the preferred low-cost capable runtime, change the `model:` values in `apex*.agent.md`; the Apex architecture and instructions remain unchanged.

Auto-approve remains disabled by default. Enable it only in trusted repositories after understanding which commands an agent can execute.

## Legacy Architect

Use for full analysis and modernization planning of large legacy systems.

- The strong model orchestrates and makes architecture decisions.
- Luna inventories and analyzes modules in isolated contexts.
- Detailed findings are stored under `docs/legacy-analysis/`.

## Hybrid Builder

Use for feature development, bug fixes, and refactoring where a strong model writes code and Luna performs research and tool-heavy work.

- The strong model decides what must be researched.
- Luna performs repository search, dependency tracing, web research, read-only database queries, terminal commands, builds, tests, and diagnostics.
- The strong model reads exact target files, makes the final decision, and writes production code.
- Luna verifies the completed change but never edits application code.

## Other architecture options

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
├── apex.agent.md
├── apex-explorer.agent.md
├── apex-researcher.agent.md
├── apex-database-worker.agent.md
├── apex-terminal-worker.agent.md
├── apex-implementer.agent.md
├── apex-verifier.agent.md
├── apex-reviewer.agent.md
├── legacy-architect.agent.md
├── luna-codebase-analyst.agent.md
├── hybrid-builder.agent.md
└── luna-research-worker.agent.md

prompts/
├── apex-start.prompt.md
├── analyze-legacy-codebase.prompt.md
└── build-with-luna-research.prompt.md

docs/
├── apex-design.md
└── legacy-analysis/

.vscode/
├── settings.apex.example.json
└── settings.example.json
```

## Database and MCP tools

Apex Database Worker is read-only by default and uses a configured database MCP server or safe command-line client when available. Because MCP tool names differ by environment, the global agent does not hard-code a company- or database-specific MCP identifier.

If your environment exposes a database MCP tool, add that exact tool or tool set to the worker's frontmatter as needed. Keep database access read-only by default: use `SELECT`, metadata inspection, sanitized aggregates, and `EXPLAIN`. Do not allow DDL, DML, administrative changes, or unnecessary production-row extraction without explicit approval.

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

Use read-only access for databases and external knowledge systems during research unless mutation is explicitly requested and authorized. Never expose secrets, credentials, customer records, card data, or production personal data to agent output.

A clean-slate rewrite should not be approved until behavioral completeness, reconciliation, audit requirements, failure recovery, parallel-run strategy, and rollback have evidence.

## License

MIT
