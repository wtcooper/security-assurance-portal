# Research notes (unverified)

Leads from the planning session of 2026-11-11. None of this has been checked. The spec must verify each item at its source before relying on it.

- **Probo** (getprobo/probo): reported as MIT; Go backend, TypeScript frontend, PostgreSQL, S3-compatible storage, GraphQL and MCP APIs. Its Compliance Portal reportedly covers per-document visibility, access requests, NDA click-sign, multiple portals, an updates feed, subprocessor filtering, custom domains, SSO and SCIM. No RFP or questionnaire engine.
- **Docling** (docling-project/docling): reported as MIT; Python library for PDF, DOCX, XLSX and PPTX. Its downloaded models carry separate licenses.
- **pgvector**: reported as PostgreSQL License.
- **Excluded:** Comp AI (AGPLv3 plus a commercial `/ee` directory) and CISO Assistant (AGPLv3).
- **Market features to match on outcomes:**
  - Loopio and Responsive: MCP servers, confidence scores, import from PDF, Word and vendor portals, a browser extension that fills portals.
  - Responsive's trust center: AI search with citations, NDAs, approvals, watermarking, document expiry, analytics.
  - Vanta and Drata trust centers: visitor AI chat and CRM-linked analytics.
- **AI-written code:** 2026 studies (Veracode, Tenzai, Georgia Tech) reportedly find authorization errors, SSRF, missing CSRF protection and missing headers most often.
- **Framework content:** SIG is licensed by Shared Assessments. CAIQ, HECVAT and VSA each have their own terms.
