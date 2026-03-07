# Source: Google Calendar Meeting

Fetches notes or transcripts from recurring Google Calendar events by meeting name.
Looks back a configurable number of days to find the most recent occurrence.

---

## MCP Tools

`mcp__google-workspace__calendar_listEvents` — find the calendar event
`mcp__google-workspace__docs_getText` — read attached Google Doc notes

---

## Parameters

For `calendar_listEvents`:
- `calendarId`: `primary`
- `timeMin`: sprint start date (ISO 8601)
- `timeMax`: sprint end date (ISO 8601)
- `q`: `meeting_name` from config (search query)
- `maxResults`: 5

For `docs_getText`:
- `documentId`: extracted from the Google Doc URL in the event description or attachments

---

## Config Fields

```yaml
- type: google-calendar-meeting
  meeting_name: "Destination Architecture Biweekly Review"
  lookback_days: 14
```

---

## Extraction Instructions

1. Call `calendar_listEvents` with the meeting name as search query, looking back `lookback_days` days
2. Find the most recent matching event within the sprint date range
3. Check the event description and attachments for Google Doc links
4. If a notes/transcript Doc is linked, call `docs_getText` to read it
5. Extract status updates, decisions, and action items from the doc content

**Extract:**
- Status updates per milestone or team mentioned in the notes
- Decisions made during the meeting
- Action items with owners
- Risks or blockers raised

**Ignore:**
- Attendee lists, logistics sections
- Duplicate content already in other sources

**Output shape:**
```
{ topic: "<milestone or topic>", status: "<update>", owner: "<name or null>", date: "<meeting date>", source: "google-calendar" }
```

---

## Notes

- If no Google Doc is attached, the event description may still contain notes — extract from there
- If multiple meetings match the search, prefer the one closest to the sprint end date
- If no event is found within the date range, return empty and note it (don't fail)
- Meeting names are matched by substring — "DA Biweekly" will match "Destination Architecture DA Biweekly Review"
