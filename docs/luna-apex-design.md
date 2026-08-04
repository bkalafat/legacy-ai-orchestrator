# Luna Apex: Research and Design Notes

Date: 2026-08-04

## Goal

Create a single GPT-5.6 Luna custom agent that maximizes task-completion quality rather than minimizing tokens. The agent should investigate, implement, test, review, and repair its own work inside GitHub Copilot Chat for VS Code.

A prompt cannot change the model's underlying intelligence or guarantee parity with another model. It can materially improve outcomes by giving the model a stronger coding harness: explicit tools, an execution loop, completion criteria, test discipline, repository context, and adversarial validation.

## Findings

### 1. Beast Mode's most useful ideas

Burke Holland created Beast Mode to address two common problems in fast coding models: low agency and low accuracy. The useful patterns are:

- keep working until the problem is actually resolved,
- use a todo list to prevent premature termination,
- investigate before editing,
- make incremental changes,
- test frequently,
- review the original intent after tests pass,
- do not claim an action without performing the tool call.

Sources:

- https://burkeholland.github.io/posts/beast-mode-3-1/
- https://gist.github.com/burkeholland/88af0249c4b6aff3820bf37898c8bacf

Luna Apex intentionally does not inherit several brittle Beast Mode rules:

- mandatory internet research for every task,
- unbounded recursive crawling,
- fixed-size reads such as always reading 2,000 lines,
- claims that every problem can be solved without user input,
- excessive meta-cognitive narration,
- automatic environment-file creation.

Those rules can increase context noise, create unsafe assumptions, and distract the model from decision-relevant evidence.

### 2. The harness matters, not only the model

VS Code describes an agent as a model plus a coding harness. Context assembly, available tools, the think-act-observe loop, tool results, and loop-control behavior directly affect quality.

Source:

- https://code.visualstudio.com/blogs/2026/05/15/agent-harnesses-github-copilot-vscode

This supports using a highly explicit operational loop for Luna instead of relying on motivational phrases such as "think harder."

### 3. Current custom-agent format

The current VS Code custom-agent format uses `.agent.md` files and supports:

- `name`
- `description`
- `argument-hint`
- `tools`
- `agents`
- `model`
- `user-invocable`
- `disable-model-invocation`
- `target`
- optional handoffs and hooks

Luna Apex uses documented fields only, targets VS Code, pins GPT-5.6 Luna, explicitly lists its tools, and prevents subagent use with `agents: []`.

Sources:

- https://code.visualstudio.com/docs/agent-customization/custom-agents
- https://github.com/github/awesome-copilot/blob/main/instructions/agents.instructions.md

### 4. Current VS Code quality guidance

Recent official guidance emphasizes:

- clear outcomes and acceptance criteria,
- breaking complex work into verifiable steps,
- planning before implementation,
- providing expected outputs or tests,
- running tests after changes,
- reviewing for security and edge cases,
- using checkpoints and fresh review passes,
- handling large codebases through focused exploration.

Sources:

- https://code.visualstudio.com/docs/agents/best-practices
- https://code.visualstudio.com/docs/agents/guides/context-engineering-guide
- https://code.visualstudio.com/docs/agents/concepts/agents

Luna Apex converts these recommendations into mandatory completion gates.

### 5. Test-driven and independent validation patterns

The recent VS Code TDD guide recommends red-green-refactor, focused tests, frequent execution, and verifying that a failing test fails for the intended reason. Awesome Copilot's recent agentic-evaluation and audit-integrity materials also emphasize rubric-based self-critique, retry protocols, and evidence-backed outcomes.

Sources:

- https://code.visualstudio.com/docs/agents/guides/test-driven-development-guide
- https://github.com/github/awesome-copilot/blob/main/docs/README.skills.md

Luna Apex uses test-first behavior where practical, then performs a separate adversarial review after the normal tests pass.

### 6. GPT-5.6 Luna context

GitHub describes Luna as the lightweight, fast, lowest-cost GPT-5.6 variant. GitHub Copilot supports configurable reasoning for Luna in supported clients. OpenAI's published evaluations indicate that Luna can be competitive with larger models on some agentic and professional tasks, but results vary by benchmark and do not imply universal equivalence.

Sources:

- https://github.blog/changelog/2026-07-09-openais-gpt-5-6-sol-terra-and-luna-are-now-available-in-github-copilot/
- https://docs.github.com/en/copilot/reference/ai-models/supported-models
- https://openai.com/index/gpt-5-6/

OpenAI cut Luna API pricing by roughly 80% on 2026-07-30. This makes longer autonomous loops economically attractive, but the Luna Apex design does not contain token-saving behavior.

Sources:

- https://www.reuters.com/business/retail-consumer/openai-cuts-prices-smaller-models-businesses-scrutinize-ai-spend-2026-07-30/
- https://www.axios.com/2026/07/30/openai-cuts-prices-gpt-terra-luna5

### 7. Community signals

Community discussions repeatedly highlight that:

- explicit instruction files and durable steering can matter as much as changing models,
- context pollution and compaction can cause the agent to lose intent,
- large tasks work better with clear constraints, tests, and progress tracking,
- agents drift when they receive vague, monolithic tasks,
- the final implementation needs an independent review mindset rather than trusting self-reported completion.

These are anecdotal reports, not controlled performance measurements.

Examples:

- https://www.reddit.com/r/GithubCopilot/
- https://www.reddit.com/r/ChatGPTCoding/
- https://www.reddit.com/r/ClaudeAI/comments/1rozbqb/are_agents_actually_useful_for_complex_tasks/

## Design decisions

### Single-agent architecture

Luna Apex does not use subagents. It owns investigation, coding, execution, and validation in one coherent session. This avoids delegation failure and model-selection ambiguity while keeping the workflow easy to install.

### Relevant broad tool set

The agent receives:

- `read`
- `edit`
- `search`
- `execute`
- `web`
- `todo`

The list is broad enough for end-to-end coding but excludes subagent orchestration. MCP tools can still be added for project-specific databases, browsers, APIs, or services.

### Completion contract

The agent cannot declare success until it has checked:

- requested outcome,
- acceptance criteria,
- todo completion,
- static errors,
- focused tests,
- build or type-check,
- wider regression evidence,
- final diff,
- security and data-integrity risks,
- residual unverified areas.

### Adversarial review

After normal validation passes, the agent must attempt to disprove its own solution across correctness, edge cases, security, data integrity, concurrency, compatibility, performance, maintainability, and test quality. Any credible defect reopens the work loop.

### Honest blockers

The agent is persistent but not fictional. It must stop when a genuine external blocker exists and report the exact error, attempted actions, and smallest unblocking step.

## Recommended use

1. Install `.github/agents/luna-apex.agent.md` in the target workspace.
2. Select GPT-5.6 Luna and the highest appropriate reasoning level in the VS Code model picker.
3. Select **Luna Apex** from the agent picker.
4. Use `prompts/luna-apex-start.prompt.md` for complex work.
5. Keep auto-approve disabled unless the repository and commands are trusted.
6. Increase `chat.agent.maxRequests` for long-running tasks when needed.

## Limitations

- The agent cannot guarantee the same output quality as Sol, Opus, or Fable on every task.
- A long prompt can improve process adherence but cannot add knowledge, reasoning capacity, or tool reliability that the underlying model lacks.
- Tests can be incomplete or misleading; human review remains important for high-risk production changes.
- Tool names and model identifiers can vary by VS Code, Copilot plan, extension version, and enterprise policy.
