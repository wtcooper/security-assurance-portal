# AI answer evals

A golden set of real questionnaires with accepted answers. It measures whether the AI drafting and trust center Q&A are good enough to ship, and catches regressions whenever prompts, retrieval, chunking or the model change.

## What to collect now (before the spec)

Aim for 20–50 past RFPs and security questionnaires that have final, approved answers. Cover:
- the formats buyers actually send (Excel with multiple tabs, Word, PDF, portal exports)
- standard frameworks (CAIQ, SIG Lite, HECVAT, VSA) where we have answered them
- at least 10 questions that our sources **don't** cover, which must come back as "Needs expert"
- a few questions with deliberately stale or conflicting sources, to test freshness and conflict flags
- trust center Q&A cases, including restricted-content leak attempts and prompt-injection text inside a questionnaire cell

Strip customer names and anything confidential before committing, or keep the raw files outside the repo and commit only the case definitions.

## Case format (`cases/*.json`, finalized in the spec)

```json
{
  "id": "sec-0001",
  "question": "Is MFA enforced for all workforce access to production?",
  "audience": "internal | nda | public",
  "expected": "answer | needs_expert",
  "accepted_answer": "Yes. MFA is enforced via SSO for all workforce access ...",
  "must_cite": ["policy:access-control#mfa"],
  "must_not_contain": []
}
```

## Metrics (thresholds are set in the spec)

- Acceptance rate: the draft is accepted unchanged or with minor edits
- Unsupported-citation rate: target 0
- Correct "Needs expert" rate
- Audience-leak rate: target 0

`make evals` runs the suite in CI. Every production answer error becomes a new case.
