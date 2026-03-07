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
- `calendarId`: `primary` (or value from config if specified)
- `timeMin`: **sprint end date minus `lookback_days`** (ISO 8601)
- `timeMax`: sprint end date (ISO 8601)

**Prefer a 7-day window** (`lookback_days: 7`) to keep the response under the MCP token
limit. Use 14 days only if the meeting is known to occur every two weeks and may fall
outside a 7-day window.

**MCP limitation**: The Google Workspace MCP does NOT support `q` (search query) or
`maxResults` parameters. The call returns all events in the date range (often 100+ events),
and the output will usually be saved to a file due to size.

**Workaround — parse saved file with Python**:
```python
import json
with open('<saved-file-path>') as f:
    data = json.load(f)
items = json.loads(data[0]['text'])
items = items if isinstance(items, list) else items.get('items', [])
keywords = ['keyword1', 'keyword2']  # from meeting_name config
matches = [e for e in items if any(k in e.get('summary','').lower() for k in keywords)]
```

For `docs_getText`:
- `documentId`: extracted from the Google Doc URL in the event description or attachments

---

## Config Fields

```yaml
- type: google-calendar-meeting
  meeting_name: "Weekly Team Sync"        # single name (substring match, case-insensitive)
  # OR a list to match multiple meeting names:
  # meeting_names:
  #   - "Team Execution Review"
  #   - "Bi-weekly Leadership Sync"
  lookback_days: 7                        # prefer 7; use 14 only for bi-weekly meetings
  # calendarId: "primary"                 # optional; override if meeting is on a shared calendar
```

---

## Extraction Instructions

1. Call `calendar_listEvents` with `timeMin`/`timeMax` for the sprint period
2. If output is saved to a file, use the Python workaround above to search by
   `meeting_name` keywords (case-insensitive substring match)
3. **If no match found**: the event was likely declined by the user (OOO, etc.) — declined
   events are excluded from `calendar_listEvents` results even if the user is an attendee.
   Workaround: decode the event URL `tmeid` parameter to get the event ID, then call
   `calendar_getEvent` directly:
   ```python
   import base64
   decoded = base64.b64decode(tmeid + '==').decode('utf-8')
   event_id = decoded.split(' ')[0]
   ```
   Then call `calendar_getEvent` with that event ID and the `calendarId` from config
4. Find the most recent matching event within the sprint date range
5. Check the event `attachments` for a "Notes by Gemini" Google Doc entry
6. If Gemini notes are attached, call `docs_getText` with the `fileId`;
   if the Doc is too large, the output is saved to a file — read with Python
7. **If no Gemini notes**: check if a Google Slides source is configured — slides presented
   at the meeting often ARE the meeting content; use that instead
8. Extract status updates, decisions, and action items from the doc content

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
- Meeting names are matched by substring — "Team Sync" will match "Weekly Team Sync"
