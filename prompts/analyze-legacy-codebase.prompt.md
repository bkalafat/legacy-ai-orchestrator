# Analyze a Legacy Codebase

Perform a complete analysis of this legacy codebase.

## Objectives

- Discover all business capabilities and business rules.
- Map modules, dependencies, data flows, and integrations.
- Separate online, scheduled, batch, and end-of-day processing.
- Identify transaction, retry, idempotency, and recovery behavior.
- Identify safe bounded contexts and modernization seams.

## Execution rules

1. First use Luna to create a repository inventory.
2. Partition the repository into small modules that fit comfortably in an independent Luna subagent context.
3. Analyze independent modules with up to four Luna subagents in parallel.
4. Store detailed reports under `docs/legacy-analysis/`.
5. Return only compact JSON summaries to the parent agent.
6. Independently verify claims involving money, transactions, data ownership, security, retries, external contracts, or migration decisions.
7. After all module reports are complete, use Luna to integrate the reports into a current-system specification and modernization options.
8. The parent architect must only resolve contradictions, evaluate evidence quality, and make final architecture decisions.
9. Do not modify application source code.
10. Treat external documentation as evidence that must be checked against executable artifacts.

## Optional external evidence

When available, include read-only access to:

- architecture documents,
- Confluence or SharePoint pages,
- database schemas and metadata,
- message definitions,
- deployment manifests,
- production-safe operational documentation.

Never provide write access to production databases or expose secrets, customer data, card data, or personal information in reports.
