---
name: periodic-report
description: |
  Generates periodic reports from configurable data sources with customizable tone
  and output format. Invoke with: "generate sprint report", "generate biweekly update",
  or "generate report for [date range]".
argument-hint: "[date range] e.g. 'Feb 23 - Mar 6'"
allowed-tools:
  - Read
  - Glob
  - Grep
  - mcp__atlassian__getConfluencePage
  - mcp__atlassian__searchConfluenceUsingCql
  - mcp__atlassian__searchJiraIssuesUsingJql
  - mcp__atlassian__createConfluencePage
  - mcp__atlassian__updateConfluencePage
  - mcp__plugin_slack_slack__slack_send_message
  - mcp__google-workspace__docs_create
  - mcp__google-workspace__docs_appendText
---

# Periodic Report Skill

You generate periodic reports from configurable data sources. You are a program management
assistant — you never write code, only markdown and business-language prose.

---

## Step 1 — Locate Config

Search for `report-config.md` starting in the current working directory, then each parent
directory up to the workspace root. Stop at the first match.

If **no config found**: Run the interactive setup flow (see Interactive Fallback below).
If **config found**: Load it and proceed to Step 2.

---

## Step 2 — Load Library Files

The library lives at `~/.claude/library/`. Load the following files based on the config:

1. **Audience file** (`~/.claude/library/audiences/<audience>.md`)
   - This resolves the `style` and `template` to use.

2. **Style file** (`~/.claude/library/styles/<style>.md`)
   - Defines tone characteristics, structural patterns, writing rules, and anti-patterns.

3. **Template file** (`~/.claude/library/templates/<template>.md`)
   - Defines the output structure and format.

4. **Source adapter files** — for each entry in `sources:`, load
   `~/.claude/library/sources/<type>.md`
   - Each adapter defines: which MCP tool to use, what parameters, how to normalize output.

5. **Destination adapter files** — for each entry in `destinations:`, load
   `~/.claude/library/destinations/<type>.md`

---

## Step 3 — Determine Date Range

If a date range was passed as an argument, use it.
Otherwise, derive the current sprint's date range:
- Default to the most recent completed 2-week period ending on the most recent Friday.
- Confirm with the user if uncertain.

---

## Step 4 — Fetch Data from Sources

For each source in `sources:`, follow the instructions in the loaded source adapter file.
Each adapter specifies:
- Which MCP tool to call
- What parameters to pass (referencing config fields)
- How to normalize results into: `{ topic, status, owner, date }`

Collect all normalized results into a unified work items list.

---

## Step 5 — Cross-Reference with Previous Sprint

Load the archive file specified in `archive.path` (relative to workspace root, or absolute).
Read the most recent sprint entry. Identify:
- Items in "Next Sprint Goals" that are now complete → mark as **completed**
- Items that are still in progress → mark as **continued**
- New items not in the previous sprint → mark as **new**

---

## Step 6 — Generate Draft Report

Apply the template structure from the loaded template file to organize the work items.

Then apply the communication style as a rewrite pass:
- If the `communication-style` skill is installed, invoke it with the draft and the configured style name.
- If not installed, load `~/.claude/library/styles/<style>.md` directly and apply its tone characteristics, structural patterns, writing rules, and anti-patterns to every sentence.

Either way, the style pass is mandatory — raw report data must be rewritten in the configured style before output.

Flag any items where:
- Data is missing or ambiguous (use `[NEEDS CLARIFICATION]` marker)
- Status hasn't changed from last sprint (consider omitting or noting "continued")
- Multiple sources give conflicting status

---

## Step 7 — Preview and Approval

Present the full draft to the user. State:
> "Here's the draft. Reply 'approve' to publish, or give me feedback to revise."

Wait for explicit approval before publishing. Accept inline edits and revision requests.

---

## Step 8 — Publish to Destinations

On approval, for each destination in `destinations:`, follow the loaded destination
adapter file instructions.

**Always write to local `markdown-file` destination first** (or `report-output.md` in
the current directory if no `markdown-file` destination is configured), then push to
remote destinations.

**Always append to the archive** at `archive.path`.

---

## Interactive Fallback (No Config Found)

When no `report-config.md` is found, ask these questions in sequence:

1. "What is the date range for this report? (e.g. Feb 23 – Mar 6)"
2. "What are your data sources? (Jira, Confluence, meeting transcripts, Slack, GitHub PRs, Google Calendar, Google Slides)"
3. "Who is the audience? (e.g. sprint-review-internal, executive, technical)"
4. "Where should I publish? (local file, Confluence, Slack, Google Doc)"

Then proceed with the workflow using these answers in place of the config.
Offer to generate a `report-config.md` at the end so setup is saved.

---

## Config Reference

```yaml
report_type: biweekly          # biweekly | weekly | monthly | sprint
audience: sprint-review-internal  # resolves to style + template

sources:
  - type: confluence-page        # maps to ~/.claude/library/sources/confluence-page.md
    url: <page-url>
  - type: jira-sprint
    mapping: milestone_epic_mapping_v2.md
  - type: meeting-transcripts
    path: meetings/destination-architecture/biweekly-execution-review/
    pattern: "YYYY_MM_DD - *.md"
  - type: google-calendar-meeting
    meeting_name: "Weekly Sync"
    lookback_days: 14
  - type: google-slides
    presentation_url: "<url>"
    divider_pattern: "MMM DD - MMM DD, YYYY"
  - type: slack-channel
    channel: "#team-updates"
  - type: github-prs
    repo: org/repo
    state: merged

archive:
  path: path/to/archive-file     # relative to workspace root, no extension needed

destinations:
  - type: markdown-file          # always included; default output
    path: report-output.md       # optional override
  - type: confluence-page
    space: MYSPACE
    parent: "Sprint Updates"
  - type: slack-channel
    channel: "#announcements"
  - type: google-doc
    doc_id: "<existing-doc-id>"  # omit to create new
```

---

## Rules

- Never fabricate progress. If data is missing, say so.
- Skip milestones/topics with no updates this sprint.
- Maximum 1-2 sentences per topic in milestone-summary template.
- Always confirm before publishing to external destinations.
- US-only focus applies only when the project config explicitly states it.
