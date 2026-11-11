# CLAUDE.md — security-assurance-portal

An open-source, self-hosted trust center and RFP/security-questionnaire response platform, sharing one approved knowledge base. The full purpose and constraints are in `intent/0001-trust-and-response-platform/intent.md`.

> **Pre-code version.** Once the first delivery slice is scaffolded, run `/init` and merge its build, test and lint commands into the sections below. Keep this file under about a page.

## How work happens here (AI-native SDLC)

1. `intent.md` → 2. `spec.md` → 3. `plan-NN-*.md` → 4. code, tests and evals → 5. PR reviewed against `REVIEW.md` → 6. findings come back as a new `intent.md`.
- **No code without an accepted plan.** Use plan mode first. Skills: `write-intent`, `write-spec`, `write-plan`.
- **One intent per PR.** Keep PRs small enough to review.
- If implementation departs from the plan, update the plan in the same commit.
- "Done" means the verification commands ran and their output is shown in the session or PR.
- Bug fixes: write a failing test first and commit it, then make it pass without editing the test.

## Hard rules

- **Permissive licenses only.** Follow `.claude/skills/license-policy`. Never copy from AGPL/GPL projects (for example Comp AI, CISO Assistant), even from memory.
- **Security baseline.** Follow `.claude/skills/security-baseline`. Every protected route has an authorization test.
- **AI answers need a cited, approved source,** and audience filtering happens before the model sees anything. See `.claude/skills/ai-answer-integrity`.
- **Keep it simple.** One app, PostgreSQL plus S3-compatible storage, no extra infrastructure without a written reason. See `.claude/skills/simplicity`.
- The project name is `security-assurance-portal`. Don't invent product branding.
- Never read, print or commit secrets (`.env*`, keys, tokens).

## Commands

_To be filled after scaffolding. Expected single-command entry points:_
- `make dev` — run locally
- `make test` — unit and integration tests; exits non-zero on failure
- `make lint` — formatter, linter and type checks
- `make evals` — AI answer evals (`evals/answers/`)
- `make license-check` — fails on any disallowed dependency license
- `make security-check` — dependency vulnerabilities and static analysis

## Architecture

_To be filled from the accepted spec.md._

## Mistakes to avoid (add one line each time a mistake repeats)

- _(none yet)_
