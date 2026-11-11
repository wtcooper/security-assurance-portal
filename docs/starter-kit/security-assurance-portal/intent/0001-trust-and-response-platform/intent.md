# Intent: security-assurance-portal — open-source trust center and response platform

- **Author:** Wade Cooper (originator), drafted with Claude
- **Status:** draft — awaiting product owner review
- **Date:** 2026-11-11
- **Project name:** `security-assurance-portal`. The name is functional and carries no product branding. Each deployment shows its own company name, for example "Acme Trust Center".
- **Next artifact:** `spec.md` in this folder, produced from this file once it is accepted

---

## Problem

Our client-facing teams spend too much time proving we are trustworthy. Every deal and renewal brings RFPs, RFIs, due-diligence questionnaires (DDQs) and security questionnaires, often with hundreds of questions. Most of the answers already exist somewhere: past responses, policies, our SOC 2 report, product docs. They are scattered, inconsistent and frequently out of date. Subject-matter experts get pulled in again and again to answer the same questions.

Buyers also want to serve themselves. They expect a trust center where they can see our certifications, download audit reports after signing an NDA, check our subprocessors, and get answers without waiting days for an email.

The commercial tools that solve this are expensive and split across two product categories:

- **Response management** (Loopio, Responsive) — Loopio lists from about $20,000/year for 10 seats and has no trust center.
- **Trust centers** (Drata/SafeBase, Vanta, Conveyor, Whistic) — usually bought as a separate product or as an add-on to a GRC suite.

Buying both means two vendors, two content stores that drift apart, and per-seat pricing that discourages us from inviting the experts we need. No permissively licensed open-source product covers both halves today. The closest, Probo (MIT), has a capable trust portal but no RFP or questionnaire response engine.

## Proposed outcome

One self-hosted, open-source application that does both jobs from a single approved knowledge base, at feature parity with the market leaders on the outcomes that matter, built and maintained by a very small team working with AI coding agents.

**What success looks like for the people using it**

1. A proposal or security lead uploads a 300-question spreadsheet and gets a cited first draft of every answer within minutes. They review only the answers flagged as uncertain and export the result in the buyer's original format.
2. A buyer visits our trust center, sees our posture, requests restricted documents, signs our NDA, and gets access after an approval — without emailing anyone.
3. An expert is asked only the questions no approved answer covers, through Slack or Teams. Their answer goes back into the library for next time.
4. Every answer we give, in a document or in the trust center, traces to an approved source with an owner and a review date. Nothing goes out that a human did not approve or that was not pre-approved for that audience.

**Capabilities expected (parity baseline).** The spec turns these into prioritized requirements. The tiers are proposals, not decisions.

*Knowledge base (shared by both halves)*
- Approved answer library: Q&A pairs, long-form content, attachments. Owners, tags, review cycles with expiry, version history, duplicate and conflict detection.
- Source documents (policies, SOC 2/ISO reports, pen-test summaries, product docs) used as grounding for AI answers. Each source carries an audience label: internal, NDA-gated or public.
- Optional sync from Google Drive, SharePoint or Confluence.

*Response management (RFPs, RFIs, DDQs, security questionnaires)*
- Import from Excel, Word, PDF and CSV. Automatic question extraction, including messy spreadsheets with merged cells and multiple tabs.
- AI first drafts grounded only in the library and sources. A citation on every answer, a confidence score, and an "insufficient evidence" outcome instead of a guess.
- Projects: assign questions to experts, due dates, review and approval workflow, comments, status dashboard.
- Export back into the original file and layout. Word and PDF export for narrative proposals.
- Expert requests and nudges through Slack and Microsoft Teams. Expert answers can be promoted into the library.
- Standard frameworks we are allowed to use (for example CAIQ) pre-mapped to library answers, subject to each framework's content license.
- Later tier: a browser extension that reads questions from buyer risk portals and fills answers back.

*Trust center (customer-facing)*
- A branded public page on our own domain: certifications, security overview, subprocessors with filtering, policies, FAQs, and an updates feed that visitors can subscribe to.
- Per-document visibility (public or restricted), access requests with approval, built-in NDA click-sign with a signature record, time-limited and revocable access, watermarked downloads.
- Visitor AI Q&A that answers only from content that particular visitor is allowed to see, with citations.
- More than one portal if we need them (for example per product or legal entity).
- Analytics: visits, views, downloads and requests by account. Later tier: a link to the CRM opportunity.

*Platform*
- SSO (OIDC/SAML) and SCIM for staff. Simple roles: admin, editor, reviewer, viewer, portal approver.
- Full audit log of approvals, access grants and exports.
- A documented API and an MCP server, so our own AI tools (Claude, Copilot) can query approved answers — both leaders shipped this in 2026.
- Integrations, prioritized in the spec: Slack, Teams, Salesforce or HubSpot, Google Drive, SharePoint.

**Measures we would use to call it done** (targets to be confirmed in the spec)
- Draft quality: on a golden set of our own past questionnaires, most AI drafts are accepted unchanged or with minor edits, and zero answers cite a source that does not support them.
- Speed: a 300-question questionnaire reaches a reviewed draft in one working day.
- Deflection: a measurable share of security reviews completed from the trust center alone.
- Maintainability: one engineer can run, upgrade and extend it as a part-time responsibility.

## Affected users and systems

- **Proposal and RFP managers** — run projects, own the response process.
- **Security, compliance and GRC team** — own security content, approve trust center access, keep evidence current.
- **Subject-matter experts** (engineering, legal, finance, product) — answer occasional questions. They need a two-minute path, not a training course.
- **Sales and account teams** — share the trust center, check request status, see analytics.
- **External buyers and their security reviewers** — use the trust center. This is the only surface exposed to the public internet.
- **Our engineering owner(s)** — deploy, operate, upgrade.
- **Systems touched:** identity provider (SSO/SCIM), Slack/Teams, CRM, document stores, e-mail, an LLM provider, object storage, and our existing compliance evidence (for example SOC 2 artifacts and policy documents).

## Constraints

**Licensing (hard constraint)**
- We build only on open-source components under permissive licenses: MIT, Apache-2.0, BSD-2/3-Clause, ISC, PostgreSQL, Zlib, MPL-2.0 only if confirmed case by case. No GPL, LGPL, AGPL, SSPL, BUSL/BSL, Elastic License, Commons Clause or "source-available" code — including transitive dependencies and any code copied from such projects.
- This rules out building on Comp AI (AGPLv3 plus a commercial enterprise directory) and CISO Assistant (AGPLv3 with a proprietary Pro edition). We must not copy code from them, even small snippets, or have an AI agent reproduce them.
- The same applies to AI model weights and bundled content. Model licenses are separate from code licenses (for example Docling's code is MIT, but its models carry their own licenses), and questionnaire frameworks such as SIG are licensed content we cannot ship.
- Probo (MIT; Go, TypeScript, PostgreSQL) is the leading candidate to reuse for the trust center.
- Dependency licenses are checked automatically in CI. Counsel confirms the policy before the first public release.

**Simplicity (hard constraint)**
- A small team plus AI agents must be able to understand the whole system. Prefer one deployable application, one primary backend language, and PostgreSQL as the only stateful service besides object storage:
  - full-text search in Postgres
  - vector search with pgvector
  - background jobs in a Postgres-backed queue
  - no Redis, Kafka, Elasticsearch or a separate vector database unless the spec proves it is needed
- Boring, well-documented technology over novel technology. Fewer, larger, readable modules over many microservices.
- Single organization per deployment (self-hosted), not a multi-tenant SaaS. This removes a whole class of tenant-isolation complexity. Multiple trust portals within one organization are still supported.
- Every capability above must earn its place. Parity means matching what users can accomplish, not cloning every screen. The spec should name anything it deliberately leaves out.

**Security (hard constraint)**
- The trust center hosts our most sensitive assurance documents on the public internet, so it must meet the security bar we claim in it: OWASP ASVS Level 2 as the baseline, a third-party penetration test before public launch, and our own trust center published on the product as proof.
- AI-generated code is treated as untrusted until reviewed. Recent studies of AI-generated code found missing authorization checks and SSRF to be the most common failures, so those get explicit tests.
- Questionnaire files and visitor questions are untrusted input to the LLM. Prompt injection must not be able to reveal restricted content or change answers.
- Customer data and documents stay in our infrastructure. The LLM provider is configurable, with no training on our data and no data retention beyond what the provider contract allows.

**AI answer integrity**
- No answer without a cited, approved source. When the evidence is insufficient, the system says so and routes the question to a human.
- A human approves every answer that leaves the building, unless the answer was pre-approved for that audience.

**Delivery**
- Built with Claude Code following the AI-native SDLC: `intent.md` → `spec.md` → `plan.md` → tests and evals → reviewed PRs. One intent per change, and humans approve at each gate.
- Our own project license: a permissive license, Apache-2.0 proposed for its patent grant (to confirm).

## Open questions

1. **Reuse strategy for the trust center.** Three options, with trade-offs for the spec to evaluate:
   - (a) **Fork Probo** and add the response engine inside it, removing or disabling the modules we don't need. One app and one stack, but we inherit a large GRC codebase and must keep merging upstream changes.
   - (b) **Run upstream Probo unmodified** for the trust center and build a small separate response app that uses Probo's GraphQL/MCP API. Free upgrades, but two apps and two user interfaces.
   - (c) **Build greenfield**, using Probo only as an MIT-licensed reference and borrowing code where useful. One codebase sized exactly to our scope, but we build the portal ourselves.

   The originator's lean is toward the simplest system to maintain. The spec should recommend one option with evidence.
2. **Backend language.** Probo is Go. The best permissive document-parsing tools (Docling) are Python. Can one language cover parsing and the app, or is a small parsing worker acceptable?
3. **LLM provider and hosting.** Which provider(s) at launch? Is a self-hosted open-weight model option required for confidential documents, or is a contracted API with zero data retention enough?
4. **Golden evaluation set.** Which 20–50 past questionnaires and RFPs can we use, with known-good answers, to measure draft quality before and after every AI change?
5. **Volume and users.** How many questionnaires and RFPs per year, how many internal users, and how many portal visitors? This determines whether the simple architecture is enough.
6. **Portal fill-back.** Which buyer portals send us the most questionnaires? Is a browser extension worth its maintenance cost in the first year, and do those portals' terms allow automated filling?
7. **Frameworks content.** Which standard questionnaires (CAIQ, SIG Lite, VSA, HECVAT) do buyers send us, and what does each license allow us to bundle or pre-map?
8. **CRM.** Salesforce or HubSpot, and how deep: links only, or opportunity-level analytics?
9. **Migration.** Do we have existing answer libraries (spreadsheets, a Loopio or Responsive export) to import on day one?
10. **Open-source release.** Will we publish the project publicly from day one, later, or keep it internal? This affects governance, contribution rules and security disclosure.
11. **Product owner and gates.** Who accepts this intent, the spec and production releases? Is there a separate tech lead for higher-risk changes?
