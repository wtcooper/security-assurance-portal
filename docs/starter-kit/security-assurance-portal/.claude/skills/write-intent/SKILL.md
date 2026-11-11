---
name: write-intent
description: Use when someone describes a new feature, change, problem or idea for security-assurance-portal, when a monitoring finding or scan result is too large for a single PR, or when asked to "capture", "write up" or "draft an intent". Produces intent/NNNN-slug/intent.md (Stage 1 of the AI-native SDLC).
---

# Write an intent.md

An intent is a proto-spec written in the originator's own terms. It records what is wanted and why, not how to build it.

## Process

1. **Interview before writing.** Ask analyst-style questions, a few at a time, until you can fill every section below:
   - Who has the problem, and how do they work around it today?
   - What does success look like, and how would we measure it?
   - What is out of scope?
   - Which constraints are hard, and which are preferences?
   - Which systems and users are affected?

   Don't invent answers. Anything still unknown goes under Open questions.
2. **One intent per change.** If the idea contains several independent changes, propose splitting it into separate intents.
3. **Use the next number.** Folder name is `intent/NNNN-short-slug/`. Check the existing folders for the highest number.
4. **Write in the originator's words.** Keep their phrasing for the problem and outcome. Don't turn it into implementation detail. Solution ideas the originator already has go under Constraints (if they are required) or Open questions (if they are only leanings).
5. **Keep it short.** One to two pages for a normal change. The platform intent (0001) is longer because it covers the whole product.

## Template

```markdown
# Intent: <name of the change>

- **Author:** <originator>, drafted with Claude
- **Status:** draft | accepted | rejected | superseded by NNNN
- **Date:** YYYY-MM-DD
- **Related:** <links to issues, incidents, earlier intents>

## Problem
## Proposed outcome
## Affected users and systems
## Constraints
## Open questions
```

## For findings from monitoring or scans (Stage 6)

Use the same template, and add an **Evidence** section after Problem containing:
- the metric or finding
- the time window
- the baseline and how far outside it the value is
- links to logs, dashboards or scan IDs

The originator is the monitoring job, and the service owner triages it.

## Gate

The product owner accepts or rejects the intent, recorded as a merged or closed PR. After acceptance, run the `write-spec` skill.
