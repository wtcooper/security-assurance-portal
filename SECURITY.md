# Security policy

## Reporting a vulnerability

Please report vulnerabilities privately through GitHub's "Report a vulnerability" button on this repository's Security tab. Do not open a public issue.

Include what you found, how to reproduce it and the version or commit affected.

## What the project provides

The application is designed to OWASP ASVS Level 2. The controls and the tests that prove them are described in `.claude/skills/security-baseline/SKILL.md`.

## What the deployer is responsible for

- A penetration test before exposing a trust center to the public internet
- TLS, encryption at rest and tested backups
- The contract and data-retention terms with their LLM provider
