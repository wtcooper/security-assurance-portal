# Handoff: security-assurance-portal scaffolding

**For:** the AI agent (and engineer) who will build this application.
**From:** the planning session that produced `security-assurance-portal.zip` (2026-11-11).
**Status:** Stage 1 of Anthropic's AI-native SDLC is drafted. No application code exists yet.

---

## 1. What you're building

An open-source, self-hosted platform that lets a company prove its security and compliance to customers. It covers two jobs that the market sells as separate products:

- **Response management**, like Loopio or Responsive: import RFPs, RFIs, DDQs and security questionnaires; draft cited answers with AI from an approved knowledge base; route gaps to experts; review and approve; export back into the buyer's original format.
- **Trust center**, like Drata/SafeBase, Vanta or Conveyor: a public, branded portal with certifications, subprocessors and policies; restricted documents behind access requests and NDA click-sign; visitor AI Q&A limited to content that visitor is allowed to see; analytics.

Both run from **one shared, approved knowledge base**. The goal is feature parity with market leaders on outcomes, built simply enough for a very small team plus AI agents to maintain.

## 2. What to treat as authoritative

| File | Authority | Your discretion |
|---|---|---|
| `intent/0001-trust-and-response-platform/intent.md` | **The key starting point.** It records what the owner wants and why. | Don't change the problem, outcomes or hard constraints without the owner's approval. You may answer or carry forward its open questions in `spec.md`. |
| All other files (skills, `CLAUDE.md`, `REVIEW.md`, the verifier agent, settings, eval README, repo README) | **Direction, not law.** They are scaffolding written before any code existed. | Modify, merge, split, rename or delete them as the build requires. Record notable changes in the PR description. |

When a scaffolding file conflicts with `intent.md`, the intent wins. When a scaffolding file conflicts with reality (for example a library changed, or a rule turns out to be impractical), fix the file.

## 3. Hard constraints (from intent.md — keep these unless the owner says otherwise)

1. **Permissive licenses only**, including transitive dependencies, copied code, AI model weights and bundled questionnaire content.
   - Allowed: MIT, Apache-2.0, BSD, ISC, PostgreSQL and similar.
   - Not allowed: GPL, LGPL, AGPL, SSPL, BUSL, Elastic, Commons Clause or source-available licenses.
   - Specifically, do not use or copy from **Comp AI** (AGPLv3 plus a commercial directory) or **CISO Assistant** (AGPLv3, proprietary Pro edition).
2. **Simplicity.**
   - One deployable application (a modular monolith).
   - PostgreSQL as the only database: full-text search, pgvector, and a Postgres-backed job queue.
   - S3-compatible object storage.
   - A single organization per deployment, with multiple trust portals allowed.
   - No Redis, Kafka, Elasticsearch or a separate vector database without a written reason.
3. **Security.**
   - OWASP ASVS Level 2 baseline.
   - A third-party pen test before the public trust center launches.
   - Treat AI-written code as untrusted until reviewed, with explicit tests for authorization, SSRF, CSRF and security headers.
   - Treat uploaded files and visitor questions as untrusted input to the LLM.
4. **AI answer integrity.**
   - No answer without a cited, approved source.
   - "Needs expert" is a valid result.
   - Audience filtering (public, NDA-approved, internal) happens before any content reaches the model.
   - A human approves anything that leaves the company.
5. **Functional naming.** The project is `security-assurance-portal`, with no product branding. Each deployment shows its own company name, for example "Acme Trust Center".

## 4. Contents of the zip

```
security-assurance-portal/
├── intent/0001-trust-and-response-platform/intent.md   ← START HERE
├── README.md                  repo readme: the SDLC stage table, repo map, the next-step prompt for spec.md
├── CLAUDE.md                  pre-code agent rules; fill in commands and architecture after scaffolding (/init)
├── REVIEW.md                  PR review passes (intent/plan, security, AI integrity, licenses, simplicity, correctness), severity, nit cap
├── THIRD_PARTY_NOTICES.md     attribution table for any copied permissive code
├── .gitignore                 secrets, env files, raw eval data
├── evals/answers/README.md    how to build the golden questionnaire eval set; draft case format and metrics
└── .claude/
    ├── settings.json          deny rules: reading .env, keys and secrets; force-push
    ├── agents/verifier.md     read-only subagent: runs proof commands, checks negative cases, reports
    └── skills/
        ├── write-intent/      Stage 1 — template and interview process for new intents (also used for monitoring findings)
        ├── write-spec/        Stage 2 — spec.md template: requirements with IDs, parity matrix, security, AI design, concerns, "deliberately not built", delivery slices
        ├── write-plan/        Stage 3 — plan-NN-*.md per delivery slice: files, order, risks, rejected alternatives, proof
        ├── license-policy/    allow and deny lists; how to verify and report licenses, including model weights and framework content
        ├── security-baseline/ ASVS L2 controls: SSO, central authz, signed URLs, upload sandboxing, SSRF, headers, audit, supply chain
        ├── simplicity/        architecture defaults, code rules, the three-question complexity check
        ├── ux-principles/     users, principles, WCAG 2.2 AA, naming and presentation rules
        └── ai-answer-integrity/ citations, audience filtering, prompt-injection hygiene, explainable confidence, eval metrics
```

`git init` was run, but nothing has been committed.

## 5. The workflow this scaffolding assumes

Anthropic's AI-native SDLC playbook (claude.com/resources/articles/the-ai-native-sdlc-playbook) defines six stages. Each one ends by committing a file that the next stage reads.

1. **Plan** → `intent.md`. *Done, awaiting the product owner's acceptance.*
2. **Design** → `spec.md` next to the intent, drafted with the policy skills applied. Concerns are flagged rather than resolved silently.
3. **Build** → one `plan-NN-*.md` per delivery slice, written in plan mode before any code. Update the plan in the same commit whenever the code departs from it.
4. **Test** → single-command checks (`make test`, `make lint`, `make evals`, `make license-check`, `make security-check`) plus the AI answer eval suite.
5. **Deploy** → PRs reviewed against `REVIEW.md`. A human code owner approves, and agents have no direct path to main or production.
6. **Maintain** → findings from monitoring and scans come back as a new `intent.md`.

You may adapt this process: lighter for small changes, heavier where risk is high. Keep the intent → spec → plan → reviewed PR chain for anything substantial.

## 6. Your first tasks

1. Read `intent.md` completely, then skim the skills.
2. Confirm with the owner which open questions they've answered. The most important are:
   - **Q1:** reuse strategy (fork Probo, run Probo unmodified alongside a separate response app, or build new)
   - **Q2:** backend language
   - **Q3:** LLM provider and hosting
   - **Q5:** expected volume
3. Produce `spec.md` using the `write-spec` skill (or your improved version of it). The repo README has a ready-made prompt.
   - Verify current facts yourself; don't trust this handoff's research.
   - Recommend one option for Q1, with evidence.
   - Break the build into independently shippable delivery slices. Suggested first slice: the knowledge base plus questionnaire import, cited drafting and export, measured against the eval set.
4. After the spec is accepted, write the plan for slice 1, scaffold the app, then run `/init` and fill in the commands and architecture in `CLAUDE.md`.

## 7. Research context from planning (verify before relying on it — current as of Nov 2026)

- **Probo** (getprobo/probo): MIT-licensed. Go backend, TypeScript frontend, PostgreSQL, S3-compatible storage, GraphQL and MCP APIs. Its Compliance Portal (renamed from "Trust Center" in July 2026) already includes:
  - per-document public or restricted visibility
  - access requests with approval
  - NDA click-sign with a signature record
  - multiple portals
  - an updates feed with email subscription
  - subprocessor filtering
  - custom domains

  It also includes SSO and SCIM. It has **no** RFP or questionnaire response engine. It is the strongest permissive base or reference for the trust center half.
- **Docling** (docling-project/docling): MIT-licensed Python library for parsing PDF, DOCX, XLSX and PPTX. Its downloaded models carry separate licenses, so check them.
- **pgvector**: PostgreSQL License (permissive).
- **Comp AI**: AGPLv3 with a commercial `/ee` directory — excluded. **CISO Assistant**: AGPLv3 — excluded. **LlamaIndex auto_rfp**: a demo that depends on hosted services, useful only as an idea reference.
- **Market leaders' 2026 features to match on outcomes:**
  - Loopio and Responsive both ship MCP servers and Microsoft 365 Copilot or ChatGPT/Claude integrations.
  - Loopio has confidence scores, import from PDFs, Word and vendor portals, and a browser extension that fills portals. It has no trust center.
  - Responsive's Trust Center has AI search with cited sources, NDAs, approvals, watermarking, document expiry and analytics.
  - Vanta's and Drata's trust centers have visitor AI chat and Salesforce-linked analytics.
- **AI-code security:** 2026 studies (Veracode, Tenzai, Georgia Tech's Vibe Security Radar) consistently find authorization errors, SSRF, missing CSRF protection and missing headers in agent-written apps. That's why the security skill requires explicit tests for them.
- **Licensed question content:** SIG is licensed content from Shared Assessments. CAIQ, HECVAT and VSA each have their own terms. Map answers to framework IDs; don't bundle question text unless the license allows it.

## 8. Ground rules for you

- Ask the owner when a decision is expensive to reverse and the intent doesn't settle it: architecture, data model, licensing, anything public-facing.
- Prefer deleting scaffolding over keeping rules nobody follows. A short `CLAUDE.md` that is enforced beats a long one that's ignored.
- Rules that must always hold belong in hooks or CI checks, not just in prose. Add them once there is code to run them against.
- Don't commit to main directly, push to remotes, or touch production without the owner's explicit approval.
