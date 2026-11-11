# security-assurance-portal

An open-source, self-hosted platform for proving security and compliance to customers. Two jobs, one approved knowledge base:

- **Response management** — drafts cited answers to RFPs, RFIs, DDQs and security questionnaires, routes gaps to experts, and exports into the buyer's original file.
- **Trust center** — a public portal with NDA-gated documents, access requests and visitor Q&A.

**Status:** planning. The intent is written; the spec is next. No application code yet.

## How this project is built

| Step | File | Approved by |
|---|---|---|
| What and why, and what "done" means | `intent/0001-trust-and-response-platform/intent.md` | Owner |
| Requirements and design | `spec.md`, next to the intent | Owner |
| Implementation plan per build step | `plan-NN-*.md`, next to the spec | Owner, before any code |
| Code, tests and evals | PR reviewed against `REVIEW.md` | Owner |

The whole intent is built end to end. There are no phased releases. The project is done when every end-to-end acceptance scenario, AI answer threshold and security bar in the intent passes in CI.

## Repository map

```
intent/0001-trust-and-response-platform/
  intent.md            what we are building and the success criteria
  research-notes.md    unverified leads for the spec to check
CLAUDE.md              working rules for AI agents
REVIEW.md              PR review policy
evals/answers/         golden set for AI answer quality
.claude/
  settings.json        permission deny rules (secrets, force-push)
  agents/verifier.md   independent "run it and report" subagent
  skills/              write-spec, write-plan, testing, license-policy,
                       security-baseline, simplicity, ux-principles, ai-answer-integrity
```

## License

MIT. All dependencies must be permissively licensed; see `.claude/skills/license-policy/SKILL.md`.
