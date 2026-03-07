# Style: Technical Product Discussion

## Overview

Use for engineering and product discussions, system design explanations, regulatory or
legal context-setting, and technical decision documentation. Precision over simplification.

---

## Tone Characteristics

- Accuracy over abstraction — use correct technical terms; don't simplify for non-engineers
- State + rationale — current state, and why it matters downstream
- Dependencies explicit — call out blocking dependencies and downstream impact
- No marketing language — no "exciting", "great progress", "moving fast"
- Concise — 2-3 sentences per item max; link to details rather than inline

---

## Structural Patterns

Messages follow: **Current state → Rationale/Impact → Next step or blocker**

- Lead with the precise current state (not "working on it", but "deployed to staging / in review / blocked on X")
- Add rationale: why this state matters, what it enables or blocks
- Close with the specific next step, dependency, or decision needed

---

## Writing Rules

1. Use precise state terms: merged, deployed, in staging, in prod, in review, awaiting approval, blocked
2. Name specific blockers: "blocked by [team] pending [specific decision]" not "blocked by alignment issues"
3. Avoid vague progress language: "progressing" → give the actual state; "almost done" → give the remaining task
4. Give dates or sprint targets, not "soon"
5. Call out downstream impact when relevant: "this unblocks [X] and is required for [Y]"

---

## Language Patterns

### Preferred state terms
- Merged / Deployed / In staging / In prod
- In review / Awaiting approval / Pending sign-off
- Blocked by [specific dependency or team]
- At risk for [date] due to [specific reason]
- Design finalized / Design in progress / Design blocked on [X]

### Avoided vague terms
- "progressing" → specify the exact state
- "almost done" → give the remaining task or percentage
- "soon" → give a date or sprint target
- "the team" → name the relevant service or squad if it matters
- "technical issue" → name the actual issue

---

## Format per Item

Each item should answer:
1. What is the current state? (1 sentence, precise)
2. What is the next concrete step or blocker? (1 sentence)

---

## Example Messages

**Status update to engineering audience:**
> Feature A: Change-state and multiple-charges flows merged; pricing integration with Team X blocked pending alignment on eligibility criteria — no ETA yet. Taskforce (Service A, Service B, Service C) driving delivery.

**Design discussion:**
> The processing logic is still open: we need to decide whether the trigger lives in Service A or Service B before implementation can start. Current proposal puts it in Service B — see [doc link] for tradeoffs. Decision needed before EOW to stay on track.

**Dependency callout:**
> Service A requires Service B to expose the data endpoint before data externalization can complete. Currently blocked; Service B estimates end of sprint. This unblocks both Service A and the downstream pipeline.

**Risk with specifics:**
> Release at risk for next milestone. Root cause: 3-week design rework on Feature A (change-state, multiple charges) plus 2 FTE attrition on Feature B. Current trajectory puts Release at next quarter. Downstream: Q3 target and Q4 target timelines under reassessment.

---

## Anti-Patterns

- "The team is making great progress on Feature X" — vague, no state
- "We're working on fixing the issue" — no specifics
- Translating technical state into business language for a technical audience: "the PR is delivered" (say "merged to main")
- Omitting blockers to sound positive — technical audiences need accurate dependency information
