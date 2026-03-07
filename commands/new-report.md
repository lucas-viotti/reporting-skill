---
description: Set up a new periodic report — guided conversation that creates report-config.md and a dedicated slash command in your project.
argument-hint: "[report name] e.g. 'troy-biweekly'"
---

# New Report Setup

You are a setup assistant for the periodic-report skill. Guide the user through configuring a new report by asking questions in sequence. Collect all answers before writing any files.

---

## Step 1 — Name the report

Ask:
> "What would you like to call this report? This becomes your slash command name (e.g. 'troy-biweekly' → /troy-biweekly, 'team-sprint' → /team-sprint)."

If the user passed a name as an argument, use it and confirm.

Normalize: lowercase, hyphens instead of spaces, no special characters.

---

## Step 2 — Report type

Ask:
> "How often does this report run? (biweekly / weekly / monthly / sprint)"

---

## Step 3 — Audience

Ask:
> "Who is the primary audience? This determines the writing style and report structure."

Present options:
| Choice | Use for |
|--------|---------|
| `sprint-review-internal` | Team sprint reviews, internal stakeholders |
| `executive` | C-level or VP-level updates |

If neither fits, default to `sprint-review-internal` and note they can add custom audiences to `~/.claude/library/audiences/` later.

---

## Step 4 — Data sources

Ask:
> "What data sources should this report pull from? List any that apply: Jira, Confluence, meeting transcripts, Google Calendar, Google Slides, Slack, GitHub PRs."

For each source the user mentions, collect the required identifier before moving on:

- **Jira** → "What's the path to your milestone/epic mapping file? (relative to the project root, e.g. `milestone_epic_mapping.md`)"
- **Confluence page** → "Paste the Confluence page URL:"
- **Meeting transcripts** → "What folder are the transcript files in? (relative to project root)" then "What's the filename pattern? (e.g. `YYYY_MM_DD - *.md`)"
- **Google Calendar** → "What's the recurring meeting name? (exact title or a unique substring)"
- **Google Slides** → "Paste the presentation URL:"
- **Slack** → "What's the channel name? (include the # prefix)"
- **GitHub PRs** → "What's the repo? (org/repo format)" then "Which PRs? (merged / open / all)"

---

## Step 5 — Archive path

Ask:
> "Where should I keep the cumulative report archive? Each approved report gets appended here for cross-sprint reference. Press enter to use the default."

Default: `reports/archive` (relative to project root, no extension needed).

---

## Step 6 — Destinations

Ask:
> "Where should the approved report be published? List any that apply: Confluence, Slack, Google Doc, local file."

Local file is always included. For each destination:

- **Confluence** → space key (e.g. `TROY`) + parent page title (e.g. `"Sprint Updates"`)
- **Slack** → channel name (include #)
- **Google Doc** → paste the doc URL or ID, or say "create new each sprint"
- **Local file** → output path (default: `report-output.md`)

---

## Step 7 — Write files

### Write `report-config.md`

Write `report-config.md` to the current working directory using the values collected above. Use commented-out blocks for sources the user did not configure, so they can uncomment later.

Format:

```yaml
report_type: <type>
audience: <audience>

sources:
  # configured sources here

archive:
  path: <archive_path>

destinations:
  - type: markdown-file
    path: report-output.md
  # additional destinations here
```

### Write the per-report slash command

Load `references/report-command-template.md` from the same directory tree as this command file. Fill in the placeholders:
- `{{report_name}}` → normalized name from Step 1
- `{{report_type}}` → from Step 2
- `{{config_path}}` → `./report-config.md` (relative to where the command will be invoked)

Write the filled template to `.claude/commands/<report-name>.md` in the current working directory. Create the `.claude/commands/` directory if it doesn't exist.

---

## Step 8 — Confirm

Tell the user:

> "Done. Here's what was created:
>
> - `report-config.md` — your report configuration
> - `.claude/commands/<report-name>.md` — your `/<report-name>` command
>
> Run `/<report-name>` (or say 'generate <report-name> report') to produce your first report.
> Edit `report-config.md` any time to add sources or destinations."
