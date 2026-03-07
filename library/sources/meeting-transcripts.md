# Source: Meeting Transcripts

Reads local meeting transcript and notes files from a directory, filtered to the sprint date range.

---

## MCP Tool

`Read` (local file reads), `Glob` (to discover matching files)

---

## Parameters

- `path`: directory path (from config, relative to workspace root)
- `pattern`: filename pattern (from config, e.g., `"YYYY_MM_DD - *.md"`)

---

## Config Fields

```yaml
- type: meeting-transcripts
  path: meetings/destination-architecture/biweekly-execution-review/
  pattern: "YYYY_MM_DD - *.md"
```

---

## Extraction Instructions

1. Use Glob to list all `.md` files in `path` matching `pattern`
2. Filter to files whose date prefix falls within the sprint date range
3. For each matching file, Read its contents
4. Look for:
   - Action items or decisions made
   - Status updates per milestone or topic
   - Risks or blockers raised
   - Named owners of work items

**Extract:**
- Any status update for a named milestone or topic
- Decisions made (mark as `decision`)
- Action items with owner names

**Ignore:**
- Generic meeting logistics (time, location, attendee list)
- Small talk or non-substantive discussion
- Duplicate information already captured from Jira/Confluence

**Output shape:**
```
{ topic: "<milestone or topic>", status: "<update text>", owner: "<name or null>", date: "<file date>", source: "transcript" }
```

---

## Notes

- Both `Transcript.md` and `Notes.md` are valid — read both if present for the same date
- Notes files are usually more curated; transcripts have more raw detail
- If no files match the date range, return empty (don't fail — other sources may cover the period)
