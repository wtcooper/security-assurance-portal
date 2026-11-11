# AI answer evals

A golden set that measures whether AI drafting and trust center Q&A meet the thresholds in `intent/0001-trust-and-response-platform/intent.md`, and catches regressions whenever prompts, retrieval, chunking or the model change.

## The set

Everything is synthetic and describes one fictional company, so it can be committed and shared. The same data is the product's demo data and the end-to-end test fixtures.

- **Sources:** the fictional company's policies, an audit report summary, a pen-test summary and product docs, each with an audience label.
- **Questionnaires:** at least 300 questions across Excel (multi-tab, merged cells), Word, PDF and CSV.
- **Accepted answers** for every covered question.
- At least 40 questions the sources **don't** cover, which must come back as "Needs expert".
- Stale and conflicting sources, to test freshness and conflict flags.
- Trust center Q&A cases, including restricted-content leak attempts and prompt-injection text inside a questionnaire cell and inside a source document.

No real customer or company data goes in this folder.

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

## Running

`make evals` runs the set against the live model and compares the scores with the committed baseline. It needs an LLM API key in the environment. A result below a threshold, or any failure on a zero-tolerance measure, fails the run.

Every reported answer error becomes a new case.
