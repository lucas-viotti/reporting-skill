# Source: Confluence Page

Fetches sprint report content from a Confluence page or space.

---

## MCP Tool

`mcp__atlassian__getConfluencePage` — for a known page URL or page ID
`mcp__atlassian__searchConfluenceUsingCql` — for discovering pages by space/label/title

---

## Parameters

For `getConfluencePage`:
- `url` or `pageId`: from `url` config field

For `searchConfluenceUsingCql` (when searching within a parent page):
- `cql`: `ancestor = "<parent-page-id>" AND title ~ "Sprint" ORDER BY created DESC`
- `limit`: 10

---

## Config Fields

```yaml
- type: confluence-page
  url: https://nubank.atlassian.net/wiki/spaces/SPACE/pages/12345/Page+Title
```

---

## Extraction Instructions

For each sprint report page found:

**Extract:**
- Page title (contains sprint date range)
- Body text — look for sections by team name or milestone heading
- For each team/milestone section:
  - What was completed this sprint
  - What is in progress
  - Blockers or risks mentioned
  - Next sprint goals

**Ignore:**
- Navigation markup, macros, page metadata
- Sections from teams outside the project scope

**Output shape:**
```
{ topic: "<team or milestone name>", status: "<completed|in-progress|blocked>", owner: "<team name>", date: "<sprint date range>", notes: "<extracted text>" }
```

---

## Notes

- If the URL points to a parent "Sprint Reports" page, use CQL to find child pages matching the date range
- Sprint report pages often have per-team sections — extract each as a separate work item
- Prefer the most recently created child page within the date range
