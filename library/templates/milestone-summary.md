# Template: Milestone Summary

Use for sprint/biweekly reports to program management and delivery audiences.
Extracted from real archive entries in `destination_architecture_updates`.

---

## Output Structure

```
### Sprint [DATE RANGE]

[OPTIONAL: One-line overall status — include only when there is a sprint-level risk or delay.
 Examples:
   "Beta testing for revolving and lateness is delayed (more details in the risks section below)"
   "Beta timeline at risk due to revolving+late design changes requiring rework."
 Omit entirely if the sprint is on track with no headline risk.]

**[Milestone name]**: [1-2 sentence business outcome]

**[Milestone name]**: [1-2 sentence business outcome]

[Repeat for each active milestone — skip milestones with no updates this sprint]

**Challenges & Path to Green**
- [Risk block — see structure below]
[Omit this section entirely if no active risks]
```

---

## Challenges & Path to Green Block

Include this section only when at least one of the following is true:
- A milestone is delayed or at risk of missing its target date
- A blocker is actively preventing progress (team, dependency, decision)
- A scope or design change has introduced rework with timeline impact
- Cascading risk affects downstream milestones

**Structure per risk:**
```
**[Milestone or capability] at risk for [date]**
- Root cause: [1–2 reasons, business language only]
- Working target: [revised date + confidence level]
- Path to green: [specific action + ETA]
```

**Example:**
> Beta milestone at risk for early April. Driven by team attrition, competing priorities, and design changes on Revolving and Lateness. Early May is the working target (medium confidence). Cascading impact on F&F (Jul '26) and D1PL (Aug '26) under assessment — updated roadmap ETA Feb 27.

---

## Rules

- Include **only active milestones** — skip if no progress this sprint.
- Maximum **1-2 sentences** per milestone.
- Opening status line is **optional** — only when there is a sprint-level risk.
- "Challenges & Path to Green" section is **optional** — only when risks are active.
- Apply the business tone to all sentences before output.

---

## Real Examples (from archive)

### On-track sprint (no opening line, no challenges section)
```
### Sprint December 29, 2025 - January 16, 2026

**Revolving**: Core capabilities well advanced, including accounting. Aligning with Financing
on design for revolving eligibility criteria control and pricing.

**Lateness**: Ongoing design for lateness (late state, cure, and payment processing),
expected to complete this sprint.

**Data Externalization**: Systems can now access DA credit card data (incl. in ETL).
```

### At-risk sprint (with opening line and challenges section)
```
### Sprint February 09 - February 20, 2026

Beta testing for revolving and lateness is delayed (more details in the risks section below)

**Revolving**: Completed design for change state and multiple charges; implementation
in progress by taskforce.

**Close complete bill**: Minimum payment warning ready for integrations. Advancing on
smaller cycles for testing flexibility.

**Lateness**: Completed design for lateness processing and fee structure; implementation
starting next sprint. Late fee propagation definitions still in progress.

**Challenges & Path to Green**
- The early April milestone for US CC Beta (specifically for Revolving + Lateness) will not
  be met. Mainly driven by team attrition, competing priorities, and design changes.
  Early May is the working target (medium confidence).
- Path to Green: Cascading impact on F&F (Jul '26) and D1PL (Aug '26) under assessment;
  updated roadmap publishing Feb 27.
```
