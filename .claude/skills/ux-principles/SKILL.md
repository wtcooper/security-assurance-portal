---
name: ux-principles
description: Use when drafting spec.md, designing screens or flows, writing UI copy, building the trust center or staff console, or reviewing UI changes. Covers UX principles, accessibility and presentation rules.
---

# UX principles

## Who we design for

- **Occasional experts** (engineers, legal, finance) answer a few questions a month. Their path from a Slack or Teams message to a submitted answer must take under two minutes, with no training.
- **Proposal and security leads** work in the tool daily. Optimize their screens for speed: keyboard shortcuts, bulk actions, filters and clear status.
- **External buyers** see only the trust center. It has to look credible, load fast, and work without an account until they need restricted content.

## Principles

1. **Show the evidence.** Every AI answer displays its citations, confidence reasons and source freshness inline. Reviewers should never have to hunt for why an answer was suggested.
2. **One obvious next action** per screen: review this answer, approve this request, assign this question.
3. **Status at a glance.** Projects show counts of drafted, needs expert, in review and approved, plus the due date.
4. **Never lose work.** Autosave, undo for bulk actions, and clear conflict messages when two people edit the same answer.
5. **Plain language.** Write "Needs an expert", not "Low retrieval confidence". Write "Access expires on 12 March", not "TTL 30d".
6. **Respect the buyer's format.** Export puts answers back in the original file, structure and cell positions.

## Accessibility

WCAG 2.2 AA for both the staff console and the trust center: keyboard navigation, visible focus, contrast, labelled form fields, and screen-reader-friendly tables. Check this in UI tests where practical.

## Naming and presentation

- The project is named `security-assurance-portal`. It is functional and unbranded. Don't invent product names, mascots or taglines.
- Buyers see the deploying company's identity, for example "Acme Trust Center", configured per portal: company name, logo, accent color, domain.
- The staff console uses plain functional labels: Library, Sources, Projects, Trust Center, Requests, Analytics, Settings.
- Visual style: neutral and professional, with system fonts and one configurable accent color. No custom code injection in portal customization.
- Tone: calm, precise, no hype. Security content states facts and dates.

## Trust center

- Fast and mostly static: the public content is cacheable, and only access requests and Q&A are dynamic.
- Mobile-friendly. Buyers often check from a phone between meetings.
