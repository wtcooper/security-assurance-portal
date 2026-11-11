# REVIEW.md — review policy

Every PR, whether written by a human or an agent, gets the same review passes. AI review findings never approve or block a PR on their own. Branch protection requires a code owner's approval, and production releases require a release manager.

## Review passes (in order)

1. **Intent and plan compliance**
   - Does the change match its `spec.md` requirement IDs and its `plan-*.md`?
   - Is the plan updated wherever the code departs from it?
   - Does the PR cover exactly one intent?
2. **Security** (against `.claude/skills/security-baseline`)
   - Authorization on every new route, resolver, job and download, each with a negative test
   - Input handling on uploads and parsing
   - No outbound requests to user-supplied URLs outside the allowlisted fetcher
   - CSRF protection and security headers
   - No secrets in the code, logs or prompts
   - Audit log entries for sensitive actions
3. **AI answer integrity** (against `.claude/skills/ai-answer-integrity`)
   - Citations are verified against source text
   - Audience filtering happens before the model sees any content
   - "Needs expert" works as a path, not a fallback guess
   - Evals were updated and run if prompts, retrieval or model configuration changed
4. **Licenses** (against `.claude/skills/license-policy`)
   - Each new dependency has a verified permissive license
   - `make license-check` passes
   - Copied code keeps its notice and is listed in `THIRD_PARTY_NOTICES.md`
5. **Simplicity** (against `.claude/skills/simplicity`)
   - No new services, infrastructure or abstractions without the complexity check answered
   - Nothing built that the spec lists as "Deliberately not built"
6. **Correctness and tests**
   - Bugs, edge cases and error handling
   - Tests prove the behavior described in the plan's "Proof" section
   - CI output is attached or linked

## Severity

- **Important** — must be fixed before merge: security or authorization gaps, license violations, answers without citations, audience leaks, data loss, broken behavior, missing tests for new behavior, drift from the spec without a plan update.
- **Nit** — optional: naming, small readability improvements, minor style issues CI doesn't catch.
- **Nit cap:** at most 3 nits per review. If there are more, pick the 3 that matter most.

## Out of scope for review comments

- Generated code (API types, ORM models, lockfiles), except to confirm it was regenerated, not hand-edited
- Anything CI already enforces: formatting, lint and type errors
- Vendored third-party code, except license notices

## Responding to review

- Tag `@claude` on a comment to have the fix made and pushed to the same PR.
- When the same mistake shows up in two PRs, add a one-line rule to `CLAUDE.md` under "Mistakes to avoid".
