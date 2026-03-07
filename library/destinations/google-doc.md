# Destination: Google Doc

Creates a new Google Doc or appends the report to an existing one.

---

## MCP Tools

`mcp__google-workspace__docs_create` — to create a new doc
`mcp__google-workspace__docs_appendText` — to append to an existing doc

---

## Config Fields

```yaml
- type: google-doc
  doc_id: "<existing-doc-id>"   # omit to create a new doc each sprint
```

---

## Write Instructions

**If `doc_id` is provided:**
1. Call `mcp__google-workspace__docs_appendText` with:
   - `documentId`: from config
   - `text`: formatted report with sprint date header
   - Add a horizontal rule or blank lines before appending to separate from prior entries

**If `doc_id` is not provided:**
1. Call `mcp__google-workspace__docs_create` with:
   - `title`: `Sprint Summary - <DATE RANGE>` (e.g., "Sprint Summary - Feb 23 - Mar 6, 2026")
   - `body`: formatted report content
2. Return the created doc URL to the user

---

## Notes

- Markdown formatting does not render in Google Docs — write plain text with newlines for structure
- Use ALL CAPS or spacing for section headers in plain text: `REVOLVING` or `--- Revolving ---`
- Always confirm with the user before creating or modifying a Google Doc
- If the doc is a shared team document, note that changes are visible immediately upon append
