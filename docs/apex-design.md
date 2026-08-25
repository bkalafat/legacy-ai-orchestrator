# Apex: Orchestrated Autonomous Coding Agent

Date: 2026-08-25

## Goal

Apex is a general-purpose, English-only GitHub Copilot custom-agent suite for high-agency software engineering. Its identity is intentionally independent of any company, repository, programming language, AI vendor, or model family.

The design goal is not to make the agent behave cheaply. Apex should use parallelism, tools, validation, repair loops, and as much reasoning as the task requires. Cost comes only from choosing a capable low-cost model in the agent configuration. When a better low-cost model becomes available, the `model:` fields can change without renaming Apex or its workers.

The current reference configuration uses `GPT-5.6 Luna (copilot)` as that runtime model.

## Architecture

```text
                         User
                           |
                           v
                    +-------------+
                    |    Apex     |
                    | Orchestrator|
                    +------+------+ 
                           |
                frame / plan / route
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
                    scoped implementation
                           |
             +-------------+-------------+
             |                           |
             v                           v
     Apex Implementer A          Apex Implementer B
       disjoint scope              disjoint scope
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
                   pass / findings
                    /           \
                  pass          repair
                   |              |
                   v              +--> Apex -> Implement -> Verify -> Review
                Result
```

Apex uses one orchestration level by default. Workers have isolated contexts and do not recursively spawn more workers unless a user deliberately enables nested subagents in the runtime.

## Why coordinator + workers

Current VS Code and GitHub Copilot guidance explicitly supports a coordinator that has the `agent` tool and an `agents:` allowlist. Hidden workers can have narrower tools, their own instructions, isolated context, and their own model configuration. Independent subagents can run in parallel.

This structure provides several advantages over a monolithic agent:

- noisy repository searches, web research, database results, and command logs stay out of the coordinator context;
- each worker has a narrow role and smaller instruction surface;
- independent tasks can execute concurrently;
- implementation and validation are separated;
- the final reviewer sees the solution from a fresh context rather than trusting the implementation narrative;
- the parent retains responsibility for synthesis and the final decision.

References:

- https://code.visualstudio.com/docs/agent-customization/custom-agents
- https://github.com/github/awesome-copilot/blob/main/website/src/content/docs/learning-hub/agents-and-subagents.md
- https://github.com/github/awesome-copilot/blob/main/agents/rug-orchestrator.agent.md

## Design choices

### 1. Stable role names, replaceable model

Agent names describe responsibilities, not the model currently executing them:

- `Apex`
- `Apex Explorer`
- `Apex Researcher`
- `Apex Database Worker`
- `Apex Terminal Worker`
- `Apex Implementer`
- `Apex Verifier`
- `Apex Reviewer`

This avoids model-name debt. Replacing Luna later is a configuration change rather than a rename or architecture migration.

### 2. Cost is not an agent behavior

Apex contains no token-saving, credit-saving, or reduced-effort instructions. It is allowed to use the work required to finish correctly. The economic policy is external: select the lowest-cost model that still provides the capability required by the suite.

There is no reliable portable frontmatter feature that automatically benchmarks models or selects the cheapest capable model at runtime. Therefore the reference repository pins today's chosen model explicitly. Update the model configuration when the preferred model changes.

### 3. Parallelism is selective

Parallelism is useful for independent work, not as a universal rule.

Good parallel candidates:

- codebase exploration and external documentation research;
- separate research questions;
- independent components with stable interfaces;
- multiple review lenses;
- non-conflicting test or diagnostic tasks.

Bad parallel candidates:

- two workers editing the same file or symbol ownership;
- work where one result determines another worker's assumptions;
- tightly coupled refactors without established interfaces;
- destructive or high-risk operations that need serialized control.

Apex prefers a few high-value parallel tasks over an uncontrolled swarm.

### 4. Trivial-task fast path

For an obvious, low-risk, local change, forcing a full worker pipeline can cost more time and context than it saves. Apex may work directly for truly trivial tasks while still validating the result. Standard and high-risk work should use the orchestration pipeline.

This differs intentionally from strict orchestrators that delegate every read, edit, and command.

### 5. Clear worker boundaries

**Apex Explorer** is repository read-only.

**Apex Researcher** is external-research read-only.

**Apex Database Worker** is database read-only by default and must refuse mutation without explicit authorization.

**Apex Terminal Worker** executes commands but does not edit source.

**Apex Implementer** owns a bounded write surface and does not self-certify completion.

**Apex Verifier** validates acceptance criteria independently and does not repair failures.

**Apex Reviewer** performs the final adversarial review and does not edit files.

These boundaries reduce hidden coupling and make failures easier to localize.

### 6. Separate verification and review

Verification asks: *Does the implementation satisfy the stated acceptance criteria and executable checks?*

Review asks: *What could still be wrong even after those checks pass?*

Apex therefore runs both. Material review findings reopen the implementation loop and invalidate affected verification evidence.

### 7. Evidence over confidence

Neither Apex nor a worker may claim success from confidence alone. Important decisions should be grounded in repository content, primary documentation, command results, database evidence, tests, or runtime behavior.

## Global installation

Project-scoped agents live in:

```text
.github/agents/
```

To make Apex available across projects for one user, copy the Apex agent files to:

```text
~/.copilot/agents/
```

For example:

```bash
mkdir -p ~/.copilot/agents
cp .github/agents/apex*.agent.md ~/.copilot/agents/
```

The repository version remains useful as the version-controlled source of truth. Re-copy or synchronize the files after updates.

## Model policy

Current reference runtime:

```text
GPT-5.6 Luna (copilot)
```

The model name is deliberately absent from agent identities and descriptions. When another model offers a better combination of low price, tool use, speed, context handling, and coding quality, replace the `model:` values in `apex*.agent.md` and keep the rest of the architecture stable.

Do not compensate for a cheaper model by reducing the Apex workflow. The purpose of the harness is to extract strong end-to-end performance from the selected model through decomposition, parallel execution, isolated context, independent verification, and repair loops.

## Safety defaults

- Auto-approval remains off by default.
- Database mutation is forbidden by default.
- Destructive commands require explicit authorization.
- Workers must not expose secrets or unnecessary personal data.
- Implementers must have non-overlapping write ownership when parallelized.
- External deployment, publish, push, merge, or infrastructure mutation requires clear authorization.

## Limitations

- Orchestration can improve execution quality but cannot guarantee parity with a larger model on every task.
- Subagent availability, model identifiers, tools, and frontmatter behavior can vary by VS Code, Copilot version, plan, rollout, and enterprise policy.
- Global installation copies files; this repository cannot automatically keep a user's `~/.copilot/agents/` directory synchronized.
- Database access depends on a configured MCP server or safe CLI client and is intentionally read-only by default.
- Parallel implementation is safe only when ownership and interfaces are truly independent.
- Tests can be incomplete or misleading; high-risk production changes still deserve human review.
