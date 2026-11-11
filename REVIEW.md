# REVIEW.md — review policy

Every PR gets the same passes, whoever wrote it. AI review never approves or blocks on its own; the owner approves every merge to main.

## Passes

1. **Intent and plan.** The change matches its spec requirement IDs and its plan. The plan is updated wherever the code departs from it.
2. **Proof.** The end-to-end scenario for the change passes, with CI output linked. New behavior has a test; a bug fix has the test that failed first. See `.claude/skills/testing`.
3. **Security** → `.claude/skills/security-baseline`. Every new route, job and download has an authorization check and a negative test.
4. **AI answer integrity** → `.claude/skills/ai-answer-integrity`. Evals were run if prompts, retrieval or model configuration changed.
5. **Licenses** → `.claude/skills/license-policy`. Every new dependency has a verified license.
6. **Simplicity** → `.claude/skills/simplicity`. Nothing new without the complexity check answered.

## Severity

- **Important** — fix before merge: security or authorization gaps, license violations, uncited answers, audience leaks, data loss, broken behavior, missing end-to-end proof, drift from the spec without a plan update.
- **Nit** — optional. At most 3 per review.

## Not reviewed

Generated code and lockfiles (confirm they were regenerated, not hand-edited), and anything CI already enforces.

When the same mistake appears in two PRs, add a line to `CLAUDE.md` under "Mistakes to avoid".
