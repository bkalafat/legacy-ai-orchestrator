# Luna Apex Starter Prompt

Use this after selecting **Luna Apex** in GitHub Copilot Chat.

```text
Complete this task end-to-end. Do not stop after planning or editing files.

Desired outcome:
[Describe the observable result.]

Constraints:
- [Languages, frameworks, compatibility, files, APIs, or rules that must be preserved.]
- Do not make unrelated changes.

Acceptance criteria:
- [Criterion 1]
- [Criterion 2]
- [Criterion 3]

Validation expectations:
- Reproduce the current failure first when this is a bug.
- Add or update regression tests where practical.
- Run focused tests, build/type-check, and relevant wider checks.
- Review the final diff and adversarially check edge cases, security, compatibility, and data integrity.

Autonomy:
- Investigate the repository and current official documentation as needed.
- Make reasonable reversible assumptions when they do not change the product decision.
- Continue repairing and validating until the completion gate passes.
- Ask me only for a genuine decision blocker or before a destructive/irreversible action.

At the end, report the outcome, important changes, exact validation performed, and any real residual risks.
```
