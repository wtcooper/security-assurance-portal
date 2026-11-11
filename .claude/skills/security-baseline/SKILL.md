---
name: security-baseline
description: Use when drafting spec.md or plan.md, designing or changing anything involving authentication, authorization, file upload or parsing, the public trust center, document downloads, LLM prompts, outbound HTTP, secrets or audit logging — and when reviewing PRs for security.
---

# Security baseline

The trust center hosts a company's most sensitive assurance documents on the public internet. A breach would undermine the product's whole purpose. Treat security requirements as hard constraints, not suggestions.

## Standard

- **OWASP ASVS Level 2** (current version) is the baseline for the whole app. The spec maps each major feature to the relevant ASVS chapters.
- **AI-written code is untrusted until reviewed.** Recent studies show AI coding agents most often get authorization logic and SSRF wrong and leave out security headers and CSRF protection. Every one of those gets an explicit test.

## Required controls

**Identity and access**
- Staff sign in through SSO (OIDC/SAML). Local passwords exist only for bootstrap and break-glass access, with MFA.
- Use deny-by-default authorization checks in one central place. Every endpoint, resolver, job and file download goes through it.
- Every protected route has a test proving an unauthorized user gets a 403/404.
- Trust center visitors are a separate identity class. They can never reach staff APIs.

**Trust center documents**
- Restricted files are served only through short-lived signed URLs, issued after an authorization check. Never expose public bucket paths.
- Each download is logged with the visitor, document, version and time. Watermark per visitor where it's enabled.
- Access grants expire and can be revoked. Revocation takes effect immediately.

**Untrusted input**
- Uploaded questionnaires (XLSX, DOCX, PDF, CSV) are untrusted:
  - parse in a sandboxed worker with size, page and time limits
  - never execute macros
  - strip external links
- No outbound HTTP to user-supplied URLs except through one allowlisted fetcher (to prevent SSRF). Block private IP ranges and cloud metadata addresses.
- Treat questionnaire text, visitor questions and source documents as untrusted LLM input. See `ai-answer-integrity`.

**Web**
- CSRF protection on every state-changing request, or SameSite=strict cookies plus origin checks.
- Strict security headers: Content-Security-Policy, HSTS, X-Content-Type-Options, frame-ancestors and Referrer-Policy.
- Rate limits on login, access requests, AI Q&A and downloads.

**Data and secrets**
- Secrets come only from environment variables or a secret manager. Never commit them, log them or put them in prompts.
- Record which documents were sent to which LLM provider in the audit log.
- Backup and restore is documented and covered by a test (acceptance scenario A13).

**Audit**
- Append-only audit log of logins, permission changes, approvals, access grants and revocations, exports and AI answer approvals. Admins can view and export it.

**Supply chain**
- Pin dependency versions with a lockfile.
- Run dependency vulnerability scanning and license checks in CI.
- Use minimal base images.

## The deployer's side (document it, don't build it)

A penetration test before going public, TLS certificates, encryption at rest, backup schedules, and the LLM provider's no-training and retention terms. The install guide lists these plainly.

## In spec.md and plan.md

Add a "Security" section that lists, for each feature: its threats (STRIDE is fine), the controls above that apply, and the tests that prove them. Flag any requirement in the intent that conflicts with this baseline as a concern rather than silently weakening the control.
