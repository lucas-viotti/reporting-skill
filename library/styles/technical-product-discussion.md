# Style: Technical Product Discussion

## Overview

Use for engineering and product discussions, system design explanations, regulatory or
legal context-setting, and technical decision documentation. Precision over simplification.
Replaces what was previously called "technical tone."

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
> Revolving: Change-state and multiple-charges flows merged; pricing integration with Financing blocked pending alignment on eligibility criteria — no ETA yet. Taskforce (CC Orchestrator, Credit Core, Billing) driving delivery.

**Design discussion:**
> The late-fee propagation logic is still open: we need to decide whether the fee trigger lives in Billing or Lateness before implementation can start. Current proposal puts it in Lateness — see [doc link] for tradeoffs. Decision needed before EOW to stay on track.

**Dependency callout:**
> DA ETL pipeline requires Credit Core to expose the account-level balance endpoint before data externalization can complete. Currently blocked; Credit Core estimates end of sprint. This unblocks both DA and the shuffle reads path.

**Risk with specifics:**
> Beta at risk for early April. Root cause: 3-week design rework on Revolving (change-state, multiple charges) plus 2 FTE attrition on Lateness since Jan. Current trajectory puts Beta at late April / early May. Downstream: F&F (Jul '26) and D1PL (Aug '26) timelines under reassessment.

---

## Anti-Patterns

- "The team is making great progress on the revolving feature" — vague, no state
- "We're working on fixing the issue" — no specifics
- Translating technical state into business language for a technical audience: "the PR is delivered" (say "merged to main")
- Omitting blockers to sound positive — technical audiences need accurate dependency information
