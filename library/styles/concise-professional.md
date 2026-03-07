# Style: Concise Professional

## Overview

Use for internal communication, Slack messages, sprint updates, and cross-team coordination.
Communicates outcomes clearly and efficiently to program management and delivery audiences.
Replaces what was previously called "business tone."

---

## Tone Characteristics

- Outcomes over activities — what was achieved, not what was done
- Specific and direct — no filler phrases ("exciting progress", "great work")
- Business language — no engineering jargon (APIs, endpoints, configs, deployments)
- Action-oriented — lead with verbs
- Concise — 1-2 sentences per item maximum

---

## Structural Patterns

Messages follow: **Status → Context → Next step** (when a next step exists)

- Open with the current state or outcome
- Add one sentence of context if needed for clarity
- End with the next concrete action or date, if applicable
- No preamble or closing pleasantries in Slack messages

---

## Writing Rules

1. Lead every milestone update with an action verb: Completed, Advancing, Delivered, Ready, Blocked
2. Use business outcome language — "Billing capability ready for validation" not "billing service deployed"
3. Include "at risk" signal prominently when applicable
4. Skip milestones with no updates
5. US-only scope: omit geography references unless the config explicitly includes them

---

## Language Patterns

### Preferred
- Completed, Delivered, Launched, Finalized
- Advancing, In progress, Ongoing
- Ready for [next step], Entering [phase]
- Blocked by, Pending, At risk, Delayed

### Avoided
- deployed, implemented, configured, integrated, refactored
- API, endpoint, MST, schema, query, database, regression
- "technical debt", "spike", "scaffolded", "bootstrapped"
- Geography names unless config includes multi-geo scope

### Status indicators
- On track: "On track for [milestone]"
- At risk: "At risk for [date]; [one-line reason]"
- Blocked: "Blocked pending [decision/dependency]"
- Complete: "Completed" or "Delivered"

---

## Example Messages

**Sprint update (Slack):**
> Revolving: Core capabilities advancing; alignment with Financing on eligibility criteria in progress.
> Lateness: Design finalized; implementation starting next sprint.
> Reversals: Authorization reversal complete; confirmations in progress.

**Blocked item:**
> Revolving at risk for April — design changes required rework on change state and multiple charges. Dedicated team driving delivery; May target (medium confidence).

**On-track summary:**
> All active milestones on track. Beta validation underway; no blockers this sprint.

---

## Anti-Patterns

- "We successfully deployed the billing service to the staging environment" → too technical
- "Great progress was made across all teams this sprint" → vague, no content
- "The team implemented the revolving schema migration" → implementation detail, not outcome
- Long paragraphs with multiple milestones embedded — use one entry per milestone
