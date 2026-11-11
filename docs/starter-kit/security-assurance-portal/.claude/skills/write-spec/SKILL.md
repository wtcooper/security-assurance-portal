---
name: write-spec
description: Use when an intent.md has been accepted and a spec is requested, or when asked to "write the spec", "design this" or "produce spec.md". Produces spec.md next to the intent (Stage 2 of the AI-native SDLC), applying the project's policy skills during drafting.
---

# Write a spec.md

The spec combines requirements and design in one pass. Policy is applied while you draft, not afterwards in review.

## Before drafting

1. Read the accepted `intent.md` in full.
2. Load and apply these skills: `license-policy`, `security-baseline`, `simplicity`, `ux-principles`, `ai-answer-integrity`.
3. Read `CLAUDE.md` and the existing code (if any), so the spec fits what already exists.
4. **Verify facts you rely on.** For each external project, library or standard you name, check its current license, release activity and the capability you're counting on. Don't rely on memory. Record where you checked.

## Rules

- Every requirement has an ID (R1, R2, …), a priority (P0 launch, P1 soon after, P2 later) and an acceptance check someone could test.
- Each constraint in the intent is met explicitly, or listed as a concern.
- Each open question from the intent is answered, or carried forward with an owner.
- **Flag concerns; don't resolve them silently.** This applies especially where policies conflict, such as feature parity against simplicity. The product owner resolves each concern with its policy owner.
- Make the simplest design that meets the requirement. Anything left out on purpose goes under "Deliberately not built".
- Keep it implementable: an engineer who never saw the conversation should understand what to build. Leave file-by-file detail to `plan.md`.

## Template

```markdown
# Spec: <name>
From: intent.md (<author>, <date>). Status: ready for product owner review.
Skills applied: license-policy, security-baseline, simplicity, ux-principles, ai-answer-integrity.

## Summary
## Requirements            (table: ID, priority, requirement, acceptance check)
## Parity matrix           (capability → which leader has it → our requirement ID or "not built: reason")
## Architecture            (components, data model outline, key flows; a diagram in Mermaid is welcome)
## Reused open-source components   (table: component, purpose, license, verified from)
## Security                (per feature: threats, controls, proving tests)
## AI answer design        (retrieval, audience filtering, citations, confidence, evals and thresholds)
## UX                      (key screens and flows, written as steps)
## How each constraint in the intent is met
## Open questions from the intent  (answered / carried forward with owner)
## Concerns                (each with policy owner and proposed resolution)
## Deliberately not built
## Delivery slices         (ordered, independently shippable increments; each one later becomes a plan.md)
```

## After drafting

Commit `spec.md` next to `intent.md` in the same folder, so the pair records what was asked and what was decided. The product owner reviews it (with the tech lead for higher-risk items). Accepting the spec starts plan mode for the first delivery slice.
