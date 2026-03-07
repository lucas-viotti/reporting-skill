# Style: Executive Summary

## Overview

Use for C-level and VP audiences, decision summaries, meeting wrap-ups, and escalations.
Lead with risk. Every sentence answers: "Should I be worried? What's the ask?"

---

## Tone Characteristics

- Bottom line up front — open with overall status (green/yellow/red), then context
- Risk-first — if something is at risk, it goes in the first sentence
- No operational detail — no team names, tool names, process specifics
- Decision-ready — state required decisions or escalations explicitly
- Extreme concision — one sentence per bullet; no prose paragraphs

---

## Structural Patterns

Messages follow: **Status → Risk signal → Ask (if any)**

- One-line overall status opens the message
- Bullets for each at-risk item (at-risk items first)
- Bullets for on-track items (brief)
- **Synthesis sentence** before closing: one sentence that wraps up the overall picture ("All in all, delivery is on track with one active risk")
- Closing line: escalation ask, or "no action needed this [timeframe]"

For structured readouts (post-meeting summaries, escalation updates):
- Use a backtick header: `` `Readout on [topic]` ``
- Numbered top-level points; lettered sub-bullets (a, b, c) for detail
- **Bold only the key term** within a bullet — not the full line
- Name owners explicitly ("[Name] owns X; [Name] to follow up on Y")

---

## Writing Rules

1. Open with green/yellow/red status + one-line headline
2. Risk items first — if something is delayed or at risk, it leads
3. No sub-bullets beyond 1 level in status updates; structured readouts may use 2 levels
4. State the specific ask when escalation is needed ("decision needed on X by Y")
5. Omit operational context — no team names, sprint rituals, engineering terms
6. **Bold the key risk term or noun** within a bullet, not the full line
7. Own your assessment directly — "I heard humility and openness" not "the meeting seemed positive"
8. End with a synthesis sentence + explicit CTA ("Please flag questions. Thanks.")

---

## Language Patterns

### Status framing
- Green: "On track for [date/milestone]"
- Yellow: "At risk — [one-line reason] — Path to Green: [one action]"
- Red: "Off track — [impact] — escalation needed: [ask]"

### Preferred
- "Release timeline at risk"
- "Two milestones delayed"
- "No executive action needed this sprint"
- "Decision needed: [specific topic] by [date]"
- "Updated roadmap publishing [date]"

### Avoided
- Team names, service names, engineering terminology
- Sprint jargon ("epic", "story", "PR", "deploy", "sprint")
- More than 2 levels of nesting
- Operational process detail

---

## Example Messages

**Executive sprint summary:**
> Release timeline at risk — Feature A and Feature B delayed to next milestone; no other milestones affected.
>
> - Feature A: At risk. Next milestone target (was prior milestone). Dedicated team in place; design finalized.
> - Feature B: At risk. Implementation starting; timeline dependent on Feature A.
> - Feature C: On track. Staging validation underway.
> - Feature D: On track. Authorization reversal complete; confirmations in progress.
>
> No executive action needed this sprint. Updated roadmap publishing end of next week.

**Escalation request:**
> Release delivery at risk. Feature A is blocked pending a decision on eligibility criteria with Team X — this has been unresolved for 2 weeks and is now on the critical path.
>
> Ask: Can you facilitate a decision before end of week?

**Green status:**
> All milestones on track. No blockers. Release validation begins next sprint.

**Structured readout (post-meeting or partner update):**
> `Readout on [Vendor] alignment call`
>
> 1. **Vendor acknowledged the delays** and has internally committed to corrective action.
>     a. Root cause: misaligned priorities and lack of clarity on our timeline.
>     b. Remediation: dedicated technical lead assigned; weekly Red/Yellow/Green review starting next sprint.
> 2. **Ask for our team:** designate a single senior counterpart to their technical lead. [Name], do you have a preference?
> 3. Any yellow items → flag to [Name]; red items → escalate to me directly for real-time escalation.
>
> All in all, I heard genuine commitment to change — let's hold them accountable. Please flag questions. Thanks.

---

## Anti-Patterns

- Detailed implementation explanation: "The team completed the schema migration and deployed to staging with…" — too operational
- Soft openings: "This sprint the team worked hard on…" — no bottom line
- Missing the ask: flagging risk without saying what decision is needed
- Burying risk: listing on-track items before the delayed ones
