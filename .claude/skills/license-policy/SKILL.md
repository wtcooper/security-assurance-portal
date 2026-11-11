---
name: license-policy
description: Use whenever adding, upgrading, copying from or recommending any third-party code, package, container image, AI model, dataset or questionnaire content — and when drafting spec.md or plan.md sections that name dependencies. Enforces the project's permissive-licenses-only rule.
---

# License policy: permissive only

This project ships under the MIT license, so every component we depend on or copy from must be permissively licensed too.

## Allowed

MIT, Apache-2.0, BSD-2-Clause, BSD-3-Clause, ISC, PostgreSQL License, Zlib, Unlicense, CC0-1.0, 0BSD.

## Allowed as unmodified dependencies only

LGPL (any version) and MPL-2.0, when installed through a package manager and used as published. Never copy, vendor, patch or fork their code into this repo. List each one in the dependency table so it is visible.

## Not allowed (including transitive dependencies)

GPL (any version), AGPL, SSPL, BUSL/BSL, Elastic License, Commons Clause, the Confluent Community License, "source-available" licenses, any "non-commercial" license, CC-BY-NC, and unlicensed code (no license means all rights reserved).

Named examples to avoid: **Comp AI** (AGPLv3, plus a commercial `/ee` directory) and **CISO Assistant** (AGPLv3 community edition, proprietary Pro edition). Do not copy, port, paraphrase or reproduce their code or schemas from memory.

## Rules

1. **Check before you add.** Before proposing a dependency, state its license and where you verified it: the LICENSE file or the registry metadata. If you can't verify it, don't add it — flag it.
2. **Check transitive dependencies.** Run the project's license check (part of `make check`, once it exists). A disallowed transitive dependency blocks the change.
3. **Copied code keeps its notice.** If you copy code from an allowed project (for example Probo, MIT), keep the original copyright notice and list the source, files, license and notice in `THIRD_PARTY_NOTICES.md` (create it on first use).
4. **Models are separate.** AI model weights have their own licenses, separate from the library that loads them. Check the license of each model file (for example the layout and table models Docling downloads). Open-weight models with use restrictions (custom "community" licenses) are flagged concerns, not automatic approvals.
5. **Content is separate.** Questionnaire frameworks and templates (SIG, CAIQ, HECVAT, VSA) have their own content licenses. Don't bundle their question text unless the license allows it. Mapping our answers to framework IDs is usually fine; shipping the questions may not be.
6. **Open standards over vendor SDKs.** Prefer libraries that work against open standards (S3 API, OIDC, SAML, SCIM).
7. **When unsure, stop and flag.** Write the concern into the spec or plan under "Concerns" with the package name, version, license text found, and why it's unclear. The owner decides.

## How to report

In `spec.md` and `plan.md`, every named dependency appears in a table:

| Component | Purpose | License | Verified from |
|---|---|---|---|
