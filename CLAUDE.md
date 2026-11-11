# CLAUDE.md — security-assurance-portal

An open-source (MIT), self-hosted trust center and RFP/security-questionnaire response platform sharing one approved knowledge base. What we are building, and what "done" means, is in `intent/0001-trust-and-response-platform/intent.md`.

## How work happens

`intent.md` → `spec.md` → `plan-NN-*.md` → code and tests → PR reviewed against `REVIEW.md`. The owner approves each step.

- The whole intent is built. There are no phases or partial releases. The spec's build order exists only to keep PRs reviewable.
- No code without an accepted plan. Skills: `write-spec`, `write-plan`.
- If the code departs from the plan, update the plan in the same commit.
- "Done" means the end-to-end scenario for the change passes and its output is shown. Unit tests alone are not proof. See `.claude/skills/testing`.
- Bug fixes start with a failing test, committed first.

## Hard rules (detail lives in the skills)

- Permissive licenses only → `license-policy`
- ASVS Level 2, and an authorization test for every route, job and download → `security-baseline`
- No AI answer without a cited, approved source; audience filtering before the model → `ai-answer-integrity`
- One app, PostgreSQL plus S3-compatible storage, nothing more without a written reason → `simplicity`
- No product branding. Never read, print or commit secrets.

## Commands

_Filled in when the app is scaffolded._

- `make dev` — run locally
- `make test` — unit and integration tests
- `make e2e` — end-to-end acceptance scenarios (A1–A13 in the intent)
- `make evals` — AI answer evals against the golden set
- `make check` — lint, types, license check, dependency vulnerabilities

## Architecture

_Filled in from the accepted spec._

## Mistakes to avoid (one line each time a mistake repeats)

- _(none yet)_
