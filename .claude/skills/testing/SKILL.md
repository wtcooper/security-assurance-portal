---
name: testing
description: Use when drafting the test plan in spec.md or the Proof section of a plan, writing or changing any test, fixing a bug, adding a route, job, download or integration, or deciding whether a change is done. Defines what counts as proof in this project.
---

# Testing: prove it end to end

The success criteria in `intent.md` are the definition of done. A change is proven when the end-to-end scenario it belongs to passes, not when its unit tests pass.

## Layers

| Layer | Command | What it proves |
|---|---|---|
| Unit and integration | `make test` | Logic and module boundaries, against a real PostgreSQL. Fast feedback only. |
| End to end | `make e2e` | Acceptance scenarios A1–A14: the real app, driven through a browser or its API, with real PostgreSQL and S3-compatible storage. |
| AI answer evals | `make evals` | Answer quality on the golden set against the intent's thresholds. |
| Security | part of `make test` and `make e2e` | Authorization for every role on every route, SSRF, CSRF, security headers. |

## Rules

1. **Every spec requirement names the acceptance scenario that proves it.** A requirement with no scenario is not testable; fix the requirement or add a scenario to the intent with the owner's approval.
2. **End-to-end tests use the product the way a user does.** Sign in, click, upload, download. Don't reach into the database to set up state a user could create through the app, and don't assert on internals.
3. **No mocking of our own code.** Real database, real object storage, real job queue. Only external services are replaced.
4. **External services get local stand-ins** that follow the service's documented API (Slack, Teams, Drive, SharePoint, Confluence, Salesforce, HubSpot, identity provider, e-mail). One stand-in per service, kept in the test folder. Each integration also has a manual check against the real service, recorded in the PR that adds it.
5. **The model is recorded for end-to-end tests.** Scenarios run against a fixed set of recorded model responses so they are repeatable and need no key. `make evals` runs against the live model.
6. **Negative cases are mandatory.** Each scenario includes at least one thing that must be refused: a wrong role, an expired grant, a restricted document, an uncovered question.
7. **Tests are deterministic.** No sleeps, no reliance on test order, fixed clocks and seeds. A flaky test is a bug and is fixed, never retried or skipped.

## Regression

- Every PR runs `make check`, `make test`, `make e2e` and `make evals`. Any failure blocks the merge.
- **Golden files:** each supported questionnaire format has a committed input file and expected export. The export is compared cell by cell (or block by block for Word and PDF).
- **Eval baseline:** scores are committed. A PR that drops below a threshold, or fails any zero-tolerance measure, is blocked. Raising the baseline is a deliberate commit.
- **Route coverage:** an automatic check lists every route, job and download and fails if one has no authorization test.
- **Upgrade:** a test migrates a database from the previous schema with data present and verifies nothing is lost.
- **Bugs:** write the failing test first and commit it, then fix without editing the test. The test stays.
- Never skip, disable or weaken a test to get a green run.

## Fixtures

One fictional company is the source for everything: demo data, end-to-end fixtures and the golden eval set. Keep it in one place so the three never drift apart.
