---
name: write-plan
description: Use after a spec.md is accepted, when starting a build step, in plan mode, or when asked to "plan", "write plan.md" or "how would we implement". Produces intent/NNNN-slug/plan-NN-<step>.md. No implementation starts without an accepted plan.
---

# Write a plan.md

## Inputs

The accepted `intent.md` and `spec.md`, `CLAUDE.md`, and the current codebase. Read them in plan mode: read only, no edits.

## One plan per build step

Name the file `plan-NN-<step-slug>.md`, in the same folder as the spec, and work through the spec's "Build order" in order.

## Template

```markdown
# Plan: <step name> (from spec.md YYYY-MM-DD)
Requirements covered: R1, R4, R7
Acceptance scenarios proven: A2
Status: draft | accepted | done

## Files that change   (path → what changes and why)
## Order of work       (numbered steps, each one small enough for a reviewable commit)
## Risks               (what could break, the riskiest step, how we'll know)
## Rejected alternatives (what we considered and why not)
## Proof               (the end-to-end scenario(s) this step makes pass, the exact commands, and the new tests and evals — see the `testing` skill)
## Dependencies added  (component, license, verified from — or "none")
```

## Interrogate before accepting

Answer these in the plan itself:
- What existing behavior could this break?
- Which step is the riskiest, and why?
- Which alternative did you reject?
- Which factual claims about the code or libraries did you verify, and how?

A plan passes when an engineer who never saw the conversation could implement it from the plan alone.

## While building

When implementation departs from the plan, update the plan in the same commit. Mark the plan `done` in the PR that completes the step.
