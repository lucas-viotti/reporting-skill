# Destination: Confluence Page

Creates a new Confluence page or updates an existing one with the report content.

Supports two write paths:
- **Recommended:** MCP Atlassian tools for reading + a Clojure converter library for writing. The converter sends raw Confluence storage format HTML directly to the REST API, preserving all formatting (tables, panels, mermaid diagrams).
- **Fallback:** MCP Atlassian tools for both reading and writing. Simpler setup, but MCP write tools only accept markdown or ADF (Atlassian Document Format), which is lossy for advanced content.

---

## First-Time Setup

When the user configures a Confluence destination for the first time, guide them through these steps:

### 1. API Token

Ask: "To publish to Confluence, I need API credentials. Do you have a Confluence API token?"

If no, direct them to generate one:
- Go to https://id.atlassian.com/manage-profile/security/api-tokens
- Click "Create API token", give it a label, copy the token
- Add to shell profile (`~/.zshrc` or `~/.bashrc`):
  ```bash
  export CONFLUENCE_USER_EMAIL="your.email@company.com"
  export CONFLUENCE_API_TOKEN="your-token-here"
  ```
- Restart shell or run `source ~/.zshrc`

### 2. MCP Atlassian Tools

Verify that MCP Atlassian tools are connected (these handle OAuth automatically):
- `searchConfluenceUsingCql`
- `getConfluencePage`
- `createConfluencePage`
- `updateConfluencePage`

This is sufficient for the fallback write path. For the recommended path, continue to step 3.

### 3. Clojure Converter Library (recommended, for lossless writes)

Search your organization's GitHub repositories for a Confluence converter library (try searching for "confluency" or "confluence" in your org's repos). The library should provide these functions:
- `convert` — markdown → Confluence storage format HTML
- `create!` — create a new page via REST API
- `publish!` — update an existing page via REST API
- `upload-attachment!` — attach files (e.g., mermaid diagram sources) to a page

Also verify:
- A Clojure REPL is accessible via the `clojure_eval` MCP tool or a standalone nREPL connection
- The API token from step 1 is set (the converter uses it directly for REST API calls)

---

## Prerequisites

Before each publish operation, confirm:
- MCP Atlassian tools are available (for read operations; also for writes if using the fallback path)
- `CONFLUENCE_USER_EMAIL` and `CONFLUENCE_API_TOKEN` environment variables are set in the shell

If using the recommended path (Clojure converter):
- Clojure converter library is loaded in a REPL

---

## Tool Roles

| Operation | Recommended (Clojure converter) | Fallback (MCP only) |
|-----------|------|----------|
| **Read** | MCP: `searchConfluenceUsingCql`, `getConfluencePage` | Same |
| **Write** | REPL: `convert`, `create!`, `publish!`, `upload-attachment!` | MCP: `createConfluencePage`, `updateConfluencePage` |
| **Mermaid diagrams** | Upload attachment + `mermaid-cloud` macro | Code block with source (no rendering) |

**Why prefer the Clojure converter?** The MCP write tools accept only markdown or ADF, which causes:
- Tables get narrow layout (`data-layout="default"`) instead of wide/full-width
- Filenames like `report-config.md` in body text get auto-linked as URLs
- Panels (info/warning/note) are lost in markdown round-trips
- Mermaid diagrams are completely stripped

The Clojure converter bypasses this by sending raw storage format HTML directly to the Confluence REST API. Use it when available; fall back to MCP writes when it's not.

---

## Config Fields

```yaml
- type: confluence-page
  space: MYSPACE                        # Confluence space key
  parent: "Sprint Updates"              # parent page title
```

---

## Workflows

### Creating a New Page

#### With Clojure converter (recommended)

1. **Resolve parent page** (MCP):
   - Search via CQL: `space = "<space>" AND title = "<parent-title>"`
   - Extract the `parent-id` from the search result

2. **Start REPL** (if not running):
   - Start the Clojure REPL and require the converter namespaces
   - Create a client using the `CONFLUENCE_USER_EMAIL` and `CONFLUENCE_API_TOKEN` env vars

3. **Convert content** (REPL):
   ```clojure
   (convert client markdown-content)
   ;; => storage format HTML string
   ```

4. **Create page** (REPL):
   ```clojure
   (create! client space-key title html parent-id)
   ;; => {:page-id "12345" ...}
   ```

5. **Add mermaid diagrams** (if content has any):
   ```clojure
   ;; Upload the .mmd source as an attachment
   (upload-attachment! client page-id "diagram-name" mermaid-source)

   ;; Re-publish with updated HTML containing the mermaid-cloud macro
   (publish! client page-id title updated-html)
   ```
   See [Adding Mermaid Diagrams](#adding-mermaid-diagrams) for the exact storage format pattern.

#### With MCP tools (fallback)

1. **Resolve parent page** (MCP):
   - Search via CQL: `space = "<space>" AND title = "<parent-title>"`
   - Extract the `parent-id` from the search result

2. **Create page** (MCP):
   - Call `createConfluencePage` with:
     - `spaceKey`: from config
     - `title`: page title
     - `parentId`: resolved from step 1
     - `content`: report content in **markdown** (the tool converts it internally)
   - **Note:** This path cannot send raw storage format HTML. Content will be converted by Confluence's built-in markdown parser, which may lose formatting (see [Tool Roles](#tool-roles) for known limitations).

3. **Mermaid diagrams:** Cannot be rendered natively via this path. Instead, embed the mermaid source in a code block wrapped in an expand macro with a text description. See the fallback pattern in [Images & Diagrams](#images--diagrams).

### Updating an Existing Page

#### With Clojure converter (recommended)

1. **Find the page** (MCP):
   - Search via CQL: `space = "<space>" AND title = "<page-title>"`
   - Extract the `page-id` from the result

2. **Read current content** (MCP):
   - Call `getConfluencePage` to read the existing page content
   - Identify which sections need updating

3. **Convert updated content** (REPL):
   ```clojure
   (convert client updated-markdown)
   ;; => storage format HTML string
   ```

4. **Publish update** (REPL):
   ```clojure
   (publish! client page-id title html)
   ```

#### With MCP tools (fallback)

1. **Find the page** (MCP):
   - Search via CQL: `space = "<space>" AND title = "<page-title>"`
   - Extract the `page-id` from the result

2. **Read current content** (MCP):
   - Call `getConfluencePage` to read the existing page content

3. **Update page** (MCP):
   - Call `updateConfluencePage` with the `page-id` and updated content in **markdown**
   - Same formatting limitations as the create fallback apply

### Adding Mermaid Diagrams

Mermaid diagrams are rendered natively in Confluence using the `mermaid-cloud` extension. This requires the Clojure converter path (for attachment upload).

#### With Clojure converter

1. **Upload** the mermaid source text as a page attachment (filename should be descriptive, e.g., `pipeline-diagram`)
2. **Insert** the `mermaid-cloud` macro in the storage format HTML, referencing the attachment filename
3. **Publish** the page with the updated HTML

The storage format pattern for a mermaid-cloud macro:

```xml
<ac:structured-macro ac:name="mermaid-cloud">
  <ac:parameter ac:name="filename">diagram-name</ac:parameter>
  <ac:parameter ac:name="revision">1</ac:parameter>
</ac:structured-macro>
```

Supported diagram types include flowcharts (`graph LR`, `graph TD`) and sequence diagrams (`sequenceDiagram`).

#### Without Clojure converter (fallback)

Mermaid diagrams cannot be rendered natively without attachment upload. Instead, embed the mermaid source in a code block so the diagram definition is preserved for manual rendering or future conversion. See the fallback pattern in [Images & Diagrams](#images--diagrams).

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

Upload the mermaid diagram text as a page attachment and render it with the `mermaid-cloud` macro. Always wrap in an expand macro with a text description:

```xml
<ac:structured-macro ac:name="expand">
  <ac:parameter ac:name="title">Diagram: Reporting Skill Pipeline</ac:parameter>
  <ac:rich-text-body>
    <p><strong>Description:</strong> Flowchart showing 8 steps: report-config.md → Load library → Fetch data → Organize → Style rewrite → Review → Publish → Archive.</p>
    <ac:structured-macro ac:name="mermaid-cloud">
      <ac:parameter ac:name="filename">pipeline-diagram.mmd</ac:parameter>
      <ac:parameter ac:name="revision">1</ac:parameter>
    </ac:structured-macro>
  </ac:rich-text-body>
</ac:structured-macro>
```

This renders interactive diagrams natively in Confluence. The expand macro wrapper ensures future AI reads (which cannot see rendered diagrams) still get the text description.

**Fallback:** If the Clojure converter library is not available and you must use MCP write tools, embed the mermaid source in a code block with a text description instead:

```xml
<ac:structured-macro ac:name="expand">
  <ac:parameter ac:name="title">Diagram: Reporting Skill Pipeline</ac:parameter>
  <ac:rich-text-body>
    <p><strong>Description:</strong> Flowchart showing 8 steps: report-config.md → Load library → Fetch data → Organize → Style rewrite → Review → Publish → Archive.</p>
    <!-- mermaid source in code block -->
  </ac:rich-text-body>
</ac:structured-macro>
```

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
- When updating, read the existing page first and only modify the sections that need changes
- The Clojure converter library typically shifts heading levels (H1 → skipped since Confluence uses the page title, H2 → H1, etc.) — check the library's behavior for your specific setup
