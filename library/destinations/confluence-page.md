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
  space: MYSPACE                        # Confluence space key
  parent: "Sprint Updates"              # parent page title
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
   - `content`: report content in Confluence storage format (apply all conversions from the Markdown → Confluence Conversion table below)

---

## Markdown → Confluence Conversion

| Markdown | Confluence storage format |
|----------|--------------------------|
| `# Heading 1` through `###### Heading 6` | `<h1>` through `<h6>` |
| `**Bold**` | `<strong>Bold</strong>` |
| `*Italic*` | `<em>Italic</em>` |
| `` `inline code` `` | `<code>inline code</code>` |
| `[text](url)` | `<a href="url">text</a>` |
| `- Bullet` | `<ul><li>Bullet</li></ul>` |
| `1. Item` | `<ol><li>Item</li></ol>` |
| ` ```language ... ``` ` | See [Code Blocks](#code-blocks) below |
| `> Quote` | `<blockquote><p>Quote</p></blockquote>` |
| `~~Strikethrough~~` | `<del>Strikethrough</del>` |
| `---` | `<hr/>` |
| Markdown table | See [Tables](#tables) below |
| Blank line between sections | `<p> </p>` |

### Code Blocks

Fenced code blocks convert to Confluence's structured code macro with optional language highlighting:

```xml
<ac:structured-macro ac:name="code">
  <ac:parameter ac:name="language">python</ac:parameter>
  <ac:plain-text-body><![CDATA[def hello():
    print("hello world")]]></ac:plain-text-body>
</ac:structured-macro>
```

- The `language` parameter is omitted when no language is specified on the opening fence.
- Code content goes inside `<![CDATA[...]]>` — no HTML escaping needed.

### Tables

Markdown tables convert to Confluence's HTML table format. Every cell's content must be wrapped in `<p>` tags (Confluence convention):

```xml
<table>
  <tbody>
    <tr><th><p>Status</p></th><th><p>Count</p></th></tr>
    <tr><td><p>Done</p></td><td><p>12</p></td></tr>
    <tr><td><p>In Progress</p></td><td><p>5</p></td></tr>
  </tbody>
</table>
```

- The first row (before the `|---|---|` separator) becomes `<th>` header cells.
- Subsequent rows become `<td>` body cells.
- Inline formatting inside cells (bold, italic, code, links) follows the same conversion rules.

---

## Content Preservation Rules

When writing or updating a Confluence page, follow these rules to prevent information loss:

### Hyperlinks
- **Never drop or simplify hyperlinks.** Every URL in the source content must appear in the output.
- When converting markdown links `[text](url)` to storage format, always produce `<a href="url">text</a>`.
- When updating an existing page, read the current content first and preserve all existing hyperlinks — even if they are in sections you are not modifying.
- Add a **References** section at the bottom of the page with footnote-style links for all sources cited in the report (Jira issues, Confluence pages, Slack threads, PRs). Format: numbered list with descriptive text + URL.

### Images & Diagrams
- Confluence images and diagrams (mermaid, draw.io, etc.) are **invisible to LLMs** when read back via the API.
- Always wrap diagrams in an `/expand` macro with a **text description** so future AI reads can understand the content.

**Preferred (when attachment upload is available):** Upload the mermaid diagram text as a page attachment and render it with the `mermaid-cloud` macro. Always wrap in an expand macro with a text description:
  ```xml
  <ac:structured-macro ac:name="expand">
    <ac:parameter ac:name="title">Diagram: Reporting Skill Pipeline</ac:parameter>
    <ac:rich-text-body>
      <p><strong>Description:</strong> Flowchart showing 8 steps: report-config.md → Load library → Fetch data → Organize → Style rewrite → Review → Publish → Archive.</p>
      <ac:structured-macro ac:name="mermaid-cloud">
        <ac:parameter ac:name="filename">pipeline-diagram.mmd</ac:parameter>
      </ac:structured-macro>
    </ac:rich-text-body>
  </ac:structured-macro>
  ```
  This renders interactive diagrams natively in Confluence.

**Fallback (default for now):** Embed the mermaid source in a code block with a text description. Use this when MCP attachment upload tools aren't confirmed available:
  ```xml
  <ac:structured-macro ac:name="expand">
    <ac:parameter ac:name="title">Diagram: Reporting Skill Pipeline</ac:parameter>
    <ac:rich-text-body>
      <p><strong>Description:</strong> Flowchart showing 8 steps: report-config.md → Load library → Fetch data → Organize → Style rewrite → Review → Publish → Archive. Each step connects to the next with arrows. Sources (Jira, Slack, Confluence, Calendar) feed into the Fetch step.</p>
      <!-- mermaid source in code block -->
    </ac:rich-text-body>
  </ac:structured-macro>
  ```
- When creating a page that *should* have a diagram but you cannot render it programmatically, insert the expand macro with the description and a placeholder note: `[Diagram to be added manually — see mermaid source below]`, followed by the mermaid source in a code block.

### Footnotes & References
- Add reference links as a numbered list at the bottom of the page under a `## References` heading.
- Each reference should include: source type (Jira, Slack, Confluence, PR), descriptive label, and URL.
- Example:
  ```
  1. [Jira] PROJ-123 — Alpha milestone epic: https://<instance>.atlassian.net/browse/PROJ-123
  2. [Slack] #team-updates discussion on blockers: https://<workspace>.slack.com/archives/C0EXAMPLE/p1234567890
  3. [Confluence] Previous sprint summary: https://<instance>.atlassian.net/wiki/spaces/SPACE/pages/12345
  ```

---

## Notes

- Always confirm with the user before creating or updating a Confluence page
- If parent page is not found, ask the user to verify the parent page title
- Use `updateConfluencePage` only if the existing page content was created by this skill (check for sprint date match in title)
