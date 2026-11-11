# Intent: security-assurance-portal — open-source trust center and response platform

- **Owner:** Wade Cooper (accepts this intent, the spec and every merge to main)
- **Status:** accepted by the owner, 2026-11-11
- **Project name:** `security-assurance-portal`. Functional, no product branding. Each deployment shows its own company name, for example "Acme Trust Center".
- **License:** MIT. A public open-source project from day one, free for anyone to self-host. It is not a commercial product.
- **Next artifact:** `spec.md` in this folder

---

## Problem

Companies spend too much time proving they are trustworthy. Every deal and renewal brings RFPs, RFIs, due-diligence questionnaires (DDQs) and security questionnaires, often with hundreds of questions. Most answers already exist somewhere — past responses, policies, the SOC 2 report, product docs — but they are scattered, inconsistent and out of date, so the same experts answer the same questions again and again.

Buyers also want to serve themselves: see certifications, download audit reports after signing an NDA, check subprocessors, and get answers without waiting days for an email.

The commercial tools are expensive and split across two categories: response management (Loopio, Responsive) and trust centers (Drata/SafeBase, Vanta, Conveyor, Whistic). Buying both means two vendors and two content stores that drift apart. No permissively licensed open-source project covers both halves.

## Proposed outcome

One self-hosted application that does both jobs from a single approved knowledge base, matching the market leaders on what users can accomplish, and simple enough for one engineer working with AI coding agents to run and extend.

**Scope: everything below is built before the project is called done.** There are no tiers, phases or partial releases. The spec sets a build order so the work arrives in reviewable pull requests, but the order is not a release plan.

### Capabilities

*Knowledge base (shared by both halves)*
- Approved answer library: Q&A pairs, long-form content, attachments. Owners, tags, review dates with expiry, version history, duplicate and conflict detection.
- Source documents (policies, audit reports, pen-test summaries, product docs) used as grounding for AI answers. Each carries an audience label: internal, NDA-gated or public.
- Sync from Google Drive, SharePoint and Confluence.
- Import of an existing answer library from a spreadsheet.

*Response management*
- Import from Excel, Word, PDF and CSV, with automatic question extraction, including messy spreadsheets with merged cells and multiple tabs.
- AI first drafts grounded only in the library and sources: a citation on every answer, a confidence level with its reasons, and "Needs expert" instead of a guess.
- Projects: assign questions, due dates, review and approval, comments, status dashboard.
- Export back into the original file and layout. Word and PDF export for narrative proposals.
- Expert requests and reminders through Slack and Microsoft Teams. Expert answers can be promoted into the library.
- Library answers mapped to standard framework question IDs (for example CAIQ), within each framework's content license.
- A browser extension that reads questions from a buyer's web portal and fills approved answers back.

*Trust center (the only surface on the public internet)*
- A branded public page on the deployer's own domain: certifications, security overview, subprocessors with filtering, policies, FAQs, and an updates feed visitors can subscribe to by email.
- Per-document visibility (public or restricted), access requests with approval, NDA click-sign with a signature record, time-limited and revocable access, watermarked downloads.
- Visitor AI Q&A that answers only from content that visitor is allowed to see, with citations.
- More than one portal per deployment (for example per product or legal entity).
- Analytics: visits, views, downloads and requests by account, with a link to the CRM account.

*Platform*
- Staff SSO (OIDC and SAML) and SCIM. Simple roles: admin, editor, reviewer, viewer, portal approver.
- Full audit log of approvals, access grants and exports.
- A documented API and an MCP server, so AI tools can query approved answers within the caller's permissions.
- Integrations: Slack, Teams, Google Drive, SharePoint, Confluence, Salesforce and HubSpot.

## Success criteria

The project is done when every item below passes in CI on the main branch. Each scenario is an automated end-to-end test that drives the real application through a browser or its API, against a real PostgreSQL database and S3-compatible storage. Unit tests are expected but do not count as proof.

### End-to-end acceptance scenarios

| ID | Scenario | Passes when |
|---|---|---|
| A1 | **Library lifecycle.** An editor adds an answer and a source document, sets owner, audience and review date; a reviewer approves. | Only approved content is used for drafting. Past-review-date content is flagged. Version history shows each change. A duplicate and a conflicting answer are both detected. |
| A2 | **Questionnaire round trip.** A user imports a 300-question, multi-tab Excel file with merged cells, gets drafts, reviews flagged answers, approves and exports. Repeated for Word, PDF and CSV. | Every question is extracted. Every draft has a citation or is "Needs expert". The exported file matches the original cell for cell except the answer cells. |
| A3 | **Expert loop.** An uncovered question is sent to an expert in Slack and in Teams; the expert answers; the answer is promoted to the library. | The same question in a second import is answered from the library with a citation. |
| A4 | **Restricted document access.** A visitor requests a restricted document, signs the NDA, an approver approves, the visitor downloads, the grant is revoked. | The signature record exists. The download is watermarked and logged. After revocation or expiry the next request is denied immediately. |
| A5 | **Visitor Q&A boundaries.** A public visitor and an NDA-approved visitor ask the same questions, including attempts to extract restricted content and prompt-injection text. | Each gets cited answers only from content they may see. No restricted text ever appears for the public visitor. |
| A6 | **Staff identity.** Staff sign in through OIDC and through SAML; SCIM creates and removes a user; each role tries every action. | A removed user loses access at once. Every route, job and download is denied to every role that should not have it. |
| A7 | **Audit.** Run A1–A6. | Every approval, grant, revocation, export and sign-in appears in the audit log, and an admin can export it. |
| A8 | **API and MCP.** A client queries approved answers through the API and the MCP server with tokens of different roles. | Results respect role and audience exactly as the web app does. |
| A9 | **Content sync.** Documents are synced from Google Drive, SharePoint and Confluence. | They arrive as sources with an audience label and are not used until approved. A document removed upstream stops being used. |
| A10 | **Portals.** Two portals with different branding and domains are published; a visitor subscribes to updates. | Each portal shows only its own content. The subscriber receives the next update by email. |
| A11 | **Analytics and CRM.** Visits, downloads and requests are generated for two accounts. | Analytics show them by account, linked to the matching Salesforce and HubSpot account. |
| A12 | **Portal fill-back.** The browser extension is run against a sample buyer portal page. | Questions are read, approved answers are filled in, and nothing unapproved is submitted. |
| A13 | **Install and upgrade.** A fresh install from the documented single command; then an upgrade from the previous schema with data present; then backup and restore. | The seeded demo works after install. No data is lost on upgrade or restore. |

External services (Slack, Teams, Drive, SharePoint, Confluence, Salesforce, HubSpot, the identity provider, e-mail) are replaced in CI by local stand-ins that follow each service's documented API. Each integration also has a short manual check against the real service, recorded before the project is called done.

### AI answer quality

Measured on a synthetic golden set committed to this repo: a fictional company with its own policies, audit reports and at least 300 questions across Excel, Word, PDF and CSV questionnaires. The same set is the demo data. The owner confirmed these thresholds on 2026-11-11.

| Measure | Threshold |
|---|---|
| Answers citing a source that does not support them | 0 |
| Restricted content shown to an audience not entitled to it, including injection attempts | 0 |
| Uncovered questions returned as "Needs expert" | at least 95% |
| Covered questions whose draft matches the accepted answer in substance | at least 80% |
| Time to draft a 300-question questionnaire on the reference single-machine install | under 10 minutes |

These run against a live model. A fixed, recorded model response set makes scenarios A1–A13 repeatable in CI without a key.

### Regression

- The full end-to-end suite, the security suite and the AI answer evals run on every pull request. A failure blocks the merge.
- AI answer scores are compared with the last committed baseline. A drop below a threshold, or any new failure on a zero-tolerance measure, blocks the merge.
- Export fidelity is checked against committed golden files for every supported format.
- An automatic check lists every route, job and download and fails if any lacks an authorization test.
- Every bug fix starts with a failing test that stays in the suite.

### Security and quality bars

- OWASP ASVS Level 2: a checklist in the repo where every applicable item links to a passing test or a documented control.
- Explicit tests for authorization, SSRF, CSRF and security headers.
- No serious or critical accessibility violations (WCAG 2.2 AA, automated checks) on any page the end-to-end suite visits.
- The license check passes: no disallowed license among dependencies, models or bundled content.

## Users

- **Proposal and RFP managers** — run projects and own the response process.
- **Security and compliance teams** — own security content, approve trust center access.
- **Subject-matter experts** — answer occasional questions. They need a two-minute path, not training.
- **Sales and account teams** — share the trust center, check request status, see analytics.
- **External buyers** — use the trust center.
- **The deployer** — one engineer who installs, runs and upgrades it.

## Constraints

**Licensing (hard)**
- Only permissively licensed components: MIT, Apache-2.0, BSD-2/3-Clause, ISC, PostgreSQL, Zlib. No GPL, AGPL, SSPL, BUSL/BSL, Elastic License, Commons Clause or "source-available" code, including transitive dependencies and copied code.
- LGPL and MPL-2.0 are allowed only as unmodified dependencies installed through a package manager. Their code is never copied or modified in this repo.
- No code from Comp AI or CISO Assistant (both AGPLv3), not even reproduced from memory.
- The same applies to AI model weights and bundled content. Questionnaire frameworks such as SIG are licensed content and are not shipped.
- Licenses are checked automatically in CI.

**Simplicity (hard)**
- One deployable application and one primary backend language.
- PostgreSQL is the only database: full-text search, pgvector, and a Postgres-backed job queue. S3-compatible object storage for files. No Redis, Kafka, Elasticsearch or separate vector database unless the spec proves the need.
- Boring, well-documented technology. Fewer, larger, readable modules.
- One organization per deployment. No multi-tenant machinery. Several portals within the organization are supported.
- Full scope does not mean cloning every competitor screen. Each capability is met by the simplest design that passes its acceptance scenario.

**Security (hard)**
- OWASP ASVS Level 2 is the design baseline.
- AI-written code is untrusted until reviewed.
- Questionnaire files, synced documents and visitor questions are untrusted input to the LLM. Prompt injection must not reveal restricted content or change answers.
- Documents stay in the deployer's infrastructure. The LLM provider is configurable and the deployer supplies their own key.

**AI answer integrity (hard)**
- No answer without a cited, approved source. Insufficient evidence means "Needs expert", routed to a human.
- Audience filtering happens before any content reaches the model.
- A human approves every answer that leaves the company, unless it was pre-approved for that audience.

**The deployer's responsibilities (documented, not built)**
- A penetration test before putting their trust center on the public internet.
- Encryption at rest, backups, TLS certificates and their LLM provider contract.
- Their own legal review of NDA text and questionnaire content licenses.

## Assumptions (the owner can change these)

- **Size:** one organization, up to a few hundred staff users, hundreds of questionnaires a year, and public traffic one modest server can handle.
- **CRM depth:** account matching and a link to the CRM record. No opportunity-level reporting.

## Open questions for the spec

1. **Reuse.** Build new using Probo (MIT) as a reference, fork it, or run it alongside? The lean is to build new: a fork inherits a large GRC codebase and running it alongside means two apps. The spec confirms with evidence.
2. **Backend language.** One language must cover the app and document parsing. A single small parsing worker is acceptable only if no permissive library exists in the main language.
3. **LLM provider.** One provider interface. Which providers are supported out of the box, and where do embeddings come from?
4. **Framework content.** Which of CAIQ, SIG Lite, VSA and HECVAT allow shipping question IDs or text?
5. **Portal fill-back.** Which buyer portals to support, and do their terms allow automated filling?
