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
  divider_pattern: "MMM DD - MMM DD, YYYY"   # date-range format, e.g. "Feb 23 - Mar 6, 2026"
  # OR: a fixed anchor string when the deck uses single dates, e.g.:
  # divider_pattern: "Leadership bi-weekly sync"  # anchor text; date is "MMMM Do, YYYY" on same slide
```

---

## Extraction Instructions

1. Call `slides_getText` to fetch all slide text content from the presentation
2. Identify divider slides using one of two modes based on `divider_pattern`:

   **Date-range mode** (pattern contains date placeholders like `MMM DD`):
   - Find slides whose text matches the pattern as a date range
   - Select the divider whose range overlaps the sprint date range

   **Anchor mode** (pattern is a static string with no date placeholders):
   - Find all slides containing the exact anchor string (e.g. `"Leadership bi-weekly sync"`)
   - Each such slide also contains a single date in the format `"MMMM Do, YYYY"` (e.g. `"January 22nd, 2026"`)
   - Parse that date and select the slide whose date is the most recent one on or before the sprint end date

3. Extract text from all slides between the selected divider and the next divider
4. From the extracted section, pull:
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
