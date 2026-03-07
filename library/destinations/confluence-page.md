# Destination: Confluence Page

Creates a new Confluence page or updates an existing one with the report content.

---

## MCP Tools

`mcp__atlassian__createConfluencePage` — to create a new page
`mcp__atlassian__updateConfluencePage` — to update an existing page
`mcp__atlassian__searchConfluenceUsingCql` — to check if a page already exists

---

## Config Fields

```yaml
- type: confluence-page
  space: C3PDA                          # Confluence space key
  parent: "Destination Architecture Updates"  # parent page title
```

---

## Write Instructions

1. Check if a page for this sprint already exists:
   - CQL: `space = "<space>" AND title = "<page-title>" AND ancestor = "<parent-id>"`
   - Page title format: `Sprint Summary - <DATE RANGE>` (e.g., "Sprint Summary - Feb 23 - Mar 6, 2026")
2. If page exists: call `updateConfluencePage` with the new content
3. If not: call `createConfluencePage` with:
   - `spaceKey`: from config
   - `title`: `Sprint Summary - <DATE RANGE>`
   - `parentId`: resolved from parent page title via CQL search
   - `content`: report content in Confluence storage format (convert markdown headers to `<h3>`, bold to `<strong>`, bullets to `<ul>/<li>`)

---

## Markdown → Confluence Conversion

| Markdown | Confluence storage format |
|----------|--------------------------|
| `### Heading` | `<h3>Heading</h3>` |
| `**Bold**` | `<strong>Bold</strong>` |
| `- Bullet` | `<ul><li>Bullet</li></ul>` |
| Blank line between sections | `<p> </p>` |

---

## Notes

- Always confirm with the user before creating or updating a Confluence page
- If parent page is not found, ask the user to verify the parent page title
- Use `updateConfluencePage` only if the existing page content was created by this skill (check for sprint date match in title)
