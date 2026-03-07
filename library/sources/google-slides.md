# Source: Google Slides

Fetches sprint-specific content from a running historical slide deck by parsing
divider slides to locate the section matching the target date range.

---

## MCP Tool

`mcp__google-workspace__slides_getText`

---

## Parameters

- `presentationId`: extracted from `presentation_url` config field

---

## Config Fields

```yaml
- type: google-slides
  presentation_url: "https://docs.google.com/presentation/d/<ID>/edit"
  divider_pattern: "MMM DD - MMM DD, YYYY"   # e.g. "Feb 23 - Mar 6, 2026"
```

> **TODO:** Fill in `presentation_url` with the actual DA Leadership Updates deck URL before using this source.

---

## Extraction Instructions

> **STUB:** This source adapter is partially implemented. The text extraction works;
> the date-range section locating requires a TODO step.

1. Call `slides_getText` to fetch all slide text content from the presentation
2. Parse the text to find divider slides — slides whose content matches `divider_pattern`
3. Find the divider slide whose date range overlaps with the sprint date range
4. Extract text from all slides between that divider and the next divider
5. From the extracted section, pull:
   - Milestone status updates
   - Risk flags or escalations
   - Key decisions or announcements

**Extract:**
- Any milestone name followed by a status or update
- Bullet points under a "Status" or "Updates" heading
- Risk items under "Risks" or "Challenges" sections

**Ignore:**
- Table of contents slides
- Appendix or backup slides (usually after a final divider)
- Decorative or logo-only slides

**Output shape:**
```
{ topic: "<milestone or topic>", status: "<update text>", owner: null, date: "<sprint date range>", source: "google-slides" }
```

---

## Notes

- The `divider_pattern` format must match exactly how divider slides are titled in the deck
- If the deck uses a different date format (e.g., "Week of Feb 23"), update `divider_pattern` accordingly
- If no matching divider is found, return empty and log a warning — do not guess at content
- This source is **optional** — the report skill will proceed without it if the URL is not configured
