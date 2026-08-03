# Legacy AI Orchestrator

A cost-controlled two-model GitHub Copilot agent team for analyzing large legacy codebases.

- **Legacy Architect** uses a high-capability model only for decomposition, orchestration, evidence review, contradiction resolution, and architecture decisions.
- **Luna Codebase Analyst** performs repository inventory, module exploration, verification, and report generation in isolated subagent contexts.

The design combines pure-orchestrator discipline with bounded, evidence-based research.

## Architecture

```text
User
  |
  v
Legacy Architect - GPT-5.6 Sol or Claude Opus
  |
  +-- Luna inventory worker
  +-- Luna module analysts (up to 4 in parallel)
  +-- Luna verification worker
  +-- Luna integration worker
  |
  v
Evidence-backed architecture decision
```

## Repository contents

```text
.github/agents/legacy-architect.agent.md
.github/agents/luna-codebase-analyst.agent.md
.vscode/settings.example.json
prompts/analyze-legacy-codebase.prompt.md
docs/legacy-analysis/.gitkeep
```

## Installation

Copy both `.agent.md` files into the target repository under `.github/agents/`.

Open the target repository in VS Code with GitHub Copilot Chat, select **Legacy Architect** from the agent picker, and run the prompt in `prompts/analyze-legacy-codebase.prompt.md`.

## Model setup

The worker is configured for GPT-5.6 Luna. Set the parent model in `legacy-architect.agent.md` to the exact identifier shown by your VS Code model picker, for example GPT-5.6 Sol or Claude Opus.

Model identifiers can vary by Copilot entitlement and VS Code release. Prefer the exact value inserted by the VS Code model picker.

## Cost controls

- The parent does not read source files or terminal output.
- Detailed worker reports are written to disk instead of returned to the parent conversation.
- Worker tasks have explicit search, file-read, dependency-hop, and response budgets.
- Independent module analyses run in parallel, with a maximum of four workers.
- Verification is risk-triggered rather than performed on every low-risk finding.
- Nested subagents remain disabled so only the parent controls fan-out.

## Generated artifacts

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

Use read-only access for databases, SharePoint, Confluence, and other external systems during analysis. Never expose secrets, credentials, customer records, card data, or production personal data to agent output.

A clean-slate rewrite should not be approved until behavioral completeness, reconciliation, audit requirements, failure recovery, parallel-run strategy, and rollback have evidence.

## License

MIT
