# security-assurance-portal

An open-source, self-hosted platform for proving security and compliance to customers. It has two parts:

- **Response management** — drafts answers to RFPs, RFIs, DDQs and security questionnaires, with citations, from one approved knowledge base.
- **Trust center** — a customer-facing portal with NDA-gated documents, access requests and visitor Q&A.

Status: **Stage 1 (Plan).** The intent is drafted and awaiting product owner review. No code yet.

## How this project is built

We follow Anthropic's AI-native SDLC. Each stage ends by committing a file that the next stage reads, and humans approve at each gate.

| Stage | Artifact | Who produces it | Gate |
|---|---|---|---|
| 1. Plan | `intent/NNNN-*/intent.md` | Originator with Claude (`write-intent` skill) | Product owner accepts |
| 2. Design | `spec.md`, next to the intent | Claude, constrained by the policy skills (`write-spec`) | Product owner, plus tech lead for higher-risk items |
| 3. Build | `plan-NN-*.md`, code, `CLAUDE.md` updates | Engineer with Claude Code in plan mode (`write-plan`) | Engineer accepts the plan before any code |
| 4. Test | Tests, `evals/`, CI output | Toolchain and the `verifier` subagent | CI passes; eval thresholds met |
| 5. Deploy | PR reviewed against `REVIEW.md` | Claude review plus a human code owner | Branch protection; a release manager approves production |
| 6. Maintain | New `intent.md` from findings | Monitoring and scans | Service owner triages |

## Repository map

```
intent/0001-trust-and-response-platform/intent.md   # what we want and why (Stage 1)
CLAUDE.md                     # agent working rules; commands get filled in after scaffolding
REVIEW.md                     # PR review policy
THIRD_PARTY_NOTICES.md        # attribution for any copied permissive-licensed code
evals/answers/                # golden questionnaire set for AI answer quality
.claude/settings.json         # permission deny rules (secrets, force-push)
.claude/agents/verifier.md    # independent "run it and report" subagent
.claude/skills/
  write-intent/  write-spec/  write-plan/      # SDLC stage skills
  license-policy/                              # permissive licenses only
  security-baseline/                           # OWASP ASVS L2 and portal hardening
  simplicity/                                  # one app, Postgres plus S3, no extra infrastructure
  ux-principles/                               # users, accessibility, plain language
  ai-answer-integrity/                         # citations, audience filtering, evals
```

## Next steps

1. **Product owner reviews `intent.md`.** Answer what you can in the Open questions, then merge (accept) or close (reject) the PR.
2. **Start collecting the golden eval set** in `evals/answers/` (see its README). The spec needs it to set quality thresholds.
3. **Generate the spec.** Open Claude Code in this repo and run:

   > Use the write-spec skill. Read `intent/0001-trust-and-response-platform/intent.md` and produce `spec.md` in the same folder. Apply every policy skill. Verify current licenses and capabilities of any open-source project you propose, including Probo, before relying on it. Recommend one option for open question 1 with evidence. Describe clearly any areas of concern, especially where you cannot satisfy contradicting policies.

4. Review the spec and resolve its concerns. Then run `write-plan` for the first delivery slice.

## License

To be decided before the first code commit. Apache-2.0 is proposed (see open questions in the intent). All dependencies must be permissively licensed; see `.claude/skills/license-policy/SKILL.md`.
