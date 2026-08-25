# Apex Starter Prompt

Use this after selecting **Apex** in GitHub Copilot Chat.

```text
Complete this task end-to-end. Own the outcome, not just the next step.

Desired outcome:
[Describe the observable result.]

Constraints:
- [Languages, frameworks, compatibility, files, APIs, or rules that must be preserved.]
- Do not make unrelated changes.

Acceptance criteria:
- [Criterion 1]
- [Criterion 2]
- [Criterion 3]

Execution expectations:
- Investigate before making non-trivial changes.
- Decompose complex work into bounded tasks.
- Run independent research, repository exploration, database investigation, or other non-conflicting work in parallel when useful.
- Use specialized Apex workers with explicit scopes instead of polluting the main context with noisy intermediate work.
- Never give two implementation workers overlapping write ownership.
- For bugs, reproduce the failure or establish a failing regression check when practical.
- Continue through implementation, verification, adversarial review, repair, and re-verification until the completion gate passes.

Validation expectations:
- Run focused tests first, then build/type-check and wider checks when proportionate.
- Independently verify every acceptance criterion.
- Review the final diff.
- Perform a fresh adversarial review for correctness, edge cases, security, compatibility, data integrity, concurrency, and regression risk where relevant.

Autonomy:
- Use current official documentation when external facts can affect correctness.
- Make reasonable reversible assumptions when they do not change a material product decision.
- Ask me only for a genuine decision blocker or before a destructive/irreversible action.

At the end, report the outcome, important changes, exact validation performed, and real residual risks only.
```
